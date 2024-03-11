

流程图中的各个步骤如下：

1. **Vertex Data**：这是渲染过程的开始，包含了3D模型的顶点数据。
2. **Primitive Processing**：这一步对顶点数据进行初步处理。
3. **Vertex Shader**：这一步对每个顶点应用一个函数，通常用于变换顶点的位置或计算顶点的颜色。 - 可编程
4. **Primitive Assembly**：这一步将顶点组装成图元（如三角形）。
5. **Rasterizer**：这一步将图元转换为像素。
6. **Fragment Shader**：这一步对每个像素应用一个函数，通常用于计算像素的最终颜色。 - 可编程
7. **Depth & Stencil**：这一步进行深度和模板测试，决定哪些像素应该被绘制。
8. **Color Buffer Blend**：这一步将新的像素颜色和颜色缓冲区中的旧颜色进行混合。
9. **Dither**：这一步进行抖动处理，通常用于增加颜色深度。
10. **Frame Buffer**：这是渲染过程的结束，最终的2D图像存储在帧缓冲区中，等待显示到屏幕上。

```cpp
struct GouraudShader : public IShader {
    Vec3f          varying_intensity; // written by vertex shader, read by fragment shader
    mat<2, 3, float> varying_uv;      
    mat<4, 4, float> uniform_M;   //  Projection*ModelView
    mat<4, 4, float> uniform_MIT; // (Projection*ModelView).invert_transpose()


    virtual Vec4f vertex(int iface, int nthvert) {
        Vec4f gl_Vertex = embed<4>(model->vert(iface, nthvert)); // read the vertex from .obj file
        gl_Vertex = Viewport * Projection * ViewTrans * gl_Vertex;     // transform it to screen coordinates
        varying_uv.set_col(nthvert, model->uv(iface, nthvert));
        varying_intensity[nthvert] = std::max(0.f, model->normal(iface, nthvert) * light_dir); // get diffuse lighting intensity
        return gl_Vertex;
    }

    virtual bool fragment(Vec3f bar, TGAColor& color, int mode) {
        if (mode == 1) {
            float intensity = varying_intensity * bar;   // interpolate intensity for the current pixel
            Vec2f uv = varying_uv * bar;                 // interpolate uv for the current pixel
            color = model->diffuse(uv) * intensity;      // well duh
            return false;                              // no, we do not discard this pixel
        }
        else if (mode == 2) {
            Vec2f uv = varying_uv * bar;                 // interpolate uv for the current pixel
            Vec3f n = proj<3>(uniform_MIT * embed<4>(model->normal(uv))).normalize(); //法线从模型空间变换到世界空间
            Vec3f l = proj<3>(uniform_M * embed<4>(light_dir)).normalize(); // 光源方向从世界空间变换到模型空间
            float intensity = std::max(0.f, n * l);
            color = model->diffuse(uv) * intensity;
            return false;
        }
        else if (mode == 3) {
            Vec2f uv = varying_uv * bar;
            Vec3f n = proj<3>(uniform_MIT * embed<4>(model->normal(uv))).normalize();
            Vec3f l = proj<3>(uniform_M * embed<4>(light_dir)).normalize();
            Vec3f r = (n * (n * l * 2.f) - l).normalize();   // 反射光线的方向
            float spec = pow(std::max(r.z, 0.0f), model->specular(uv)); // 模型的纹理中获取的镜面反射系数
            float diff = std::max(0.f, n * l); // 漫反射的强度diff
            TGAColor c = model->diffuse(uv);
            color = c;
            // 漫反射颜色 * (漫反射强度 + 镜面反射颜色 * 镜面反射强度)
            for (int i = 0; i < 3; i++) color[i] = std::min<float>(5 + c[i] * (diff + .6 * spec), 255);
            return false;
        }
    }
};
```

着色器干的事情：加载模型->改顶点位置->算光照等等去贴颜色

phong着色模型 法线贴图 差别在于掌握的信息密度

phong着色模型使用模型中三角形网格提供的法线向量并插值，而法线贴图纹理能提供更丰富的纹理



新的纹理贴图使用Darboux帧进行存储？目的是该贴图能够适应变形，能够使得动画能够根据一张纹理贴图正确渲染，而不需要根据改变的模型改变纹理贴图



#### 关于切线空间法线贴图的代码

看不懂 所以直接放代码

```c++
struct GouraudShader : public IShader {
    mat<4, 3, float> varying_tri; // 存储三角形的每个顶点的坐标 在裁剪空间中
    mat<3, 3, float> varying_nrm; // 存储每个顶点的法向量 在世界空间中
    mat<3, 3, float> ndc_tri;     // 存储三角形的每个顶点的坐标 在（Normalized Device Coordinates，NDC）空间
    virtual Vec4f vertex(int iface, int nthvert) {
        Vec4f gl_Vertex = embed<4>(model->vert(iface, nthvert)); // read the vertex from .obj file
        gl_Vertex = Viewport * Projection * ViewTrans * gl_Vertex;    // transform it to screen coordinates
        varying_intensity[nthvert] = std::max(0.f, model->normal(iface, nthvert) * light_dir); // get diffuse lighting intensity
        
        varying_uv.set_col(nthvert, model->uv(iface, nthvert));
        varying_nrm.set_col(nthvert, proj<3>((Projection * ViewTrans).invert_transpose() * embed<4>(model->normal(iface, nthvert), 0.f)));
        varying_tri.set_col(nthvert, gl_Vertex);
        ndc_tri.set_col(nthvert, proj<3>(gl_Vertex / gl_Vertex[3]));

        return gl_Vertex;
    }
    
    virtual bool fragment(Vec3f bar, TGAColor& color, int mode) {
        Vec2f uv = varying_uv * bar;
        Vec3f bn = (varying_nrm * bar).normalize();

        mat<3, 3, float> A;
        A[0] = ndc_tri.col(1) - ndc_tri.col(0);
        A[1] = ndc_tri.col(2) - ndc_tri.col(0);
        A[2] = bn;

        mat<3, 3, float> AI = A.invert_transpose();

        Vec3f i=AI * Vec3f(varying_uv[0][1] - varying_uv[0][0], varying_uv[0][2] - varying_uv[0][0], 0);
        Vec3f j=AI * Vec3f(varying_uv[1][1] - varying_uv[1][0], varying_uv[1][2] - varying_uv[1][0], 0);

        mat<3, 3, float> B;
        B.set_col(0, i.normalize());
        B.set_col(1, j.normalize());
        B.set_col(2, bn);

        Vec3f n = (B * model->normal(uv)).normalize();

        float diff = std::max(0.f, n * light_dir);
        color = model->diffuse(uv) * diff;

        return false;
        
    }
}
```

