### 阴影贴图

进行两次渲染。第一次我们将渲染图像，将相机放置在光源位置。它将允许确定哪些部分被照亮以及哪些部分被隐藏。然后在第二遍中，考虑可见性信息进行渲染。

阴影缓冲区（shadow buffer）是用来计算阴影的。它是从光源的视角渲染的深度图，用于存储每个像素距离光源的深度信息。当渲染帧缓冲区时，会使用这个阴影缓冲区来判断每个像素是否在阴影中。

具体来说，当渲染帧缓冲区的每个像素时，会计算该像素在世界空间中的位置，然后将这个位置转换到光源的视角，得到该像素在光源视角下的深度。然后，将这个深度与阴影缓冲区中对应位置的深度进行比较。如果像素的深度大于阴影缓冲区中的深度（也就是说，像素距离光源的距离大于阴影缓冲区中的距离），那么这个像素就在阴影中。

第一个shader

```c++
struct DepthShader : public IShader{
    mat<3, 3, float> varying_tri;

    DepthShader() : varying_tri() {}

    virtual Vec4f vertex(int iface, int nthvert) {
        Vec4f gl_Vertex = embed<4>(model->vert(iface, nthvert)); // read the vertex from .obj file
        gl_Vertex = Viewport * Projection * ViewTrans * gl_Vertex;          // transform it to screen coordinates
        varying_tri.set_col(nthvert, proj<3>(gl_Vertex / gl_Vertex[3]));
        return gl_Vertex;
    }

    virtual bool fragment(Vec3f bar, TGAColor& color, int mode) {
        Vec3f p = varying_tri * bar;
        color = TGAColor(255, 255, 255) * (p.z / depth);
        return false;
    }
};
```

第二个Shader

```c++
struct SecondShader :public IShader {
    mat<4, 4, float> uniform_M;   //  Projection*ModelView
    mat<4, 4, float> uniform_MIT; // (Projection*ModelView).invert_transpose()
    mat<4, 4, float> uniform_Mshadow; // transform framebuffer screen coordinates to shadowbuffer screen coordinates
    mat<2, 3, float> varying_uv;  // triangle uv coordinates, written by the vertex shader, read by the fragment shader
    mat<3, 3, float> varying_tri; // triangle coordinates before Viewport transform, written by VS, read by FS

    SecondShader(Matrix M, Matrix MIT, Matrix MS) : uniform_M(M), uniform_MIT(MIT), uniform_Mshadow(MS), varying_uv(), varying_tri() {}

    virtual Vec4f vertex(int iface, int nthvert) {
        varying_uv.set_col(nthvert, model->uv(iface, nthvert));
        Vec4f gl_Vertex = Viewport * Projection * ViewTrans * embed<4>(model->vert(iface, nthvert));
        varying_tri.set_col(nthvert, proj<3>(gl_Vertex / gl_Vertex[3]));
        return gl_Vertex;
    }

    virtual bool fragment(Vec3f bar, TGAColor& color, int mode) {
        Vec4f sb_p = uniform_Mshadow * embed<4>(varying_tri * bar); // corresponding point in the shadow buffer
        sb_p = sb_p / sb_p[3];
        int idx = int(sb_p[0]) + int(sb_p[1]) * width; // index in the shadowbuffer array
        float shadow = .3 + .7 * (shadowbuffer[idx] < sb_p[2] + 0.5); // +0.5 to avoid z-fighting
        Vec2f uv = varying_uv * bar;
        Vec3f n = proj<3>(uniform_MIT * embed<4>(model->normal(uv))).normalize(); // 法线
        Vec3f l = proj<3>(uniform_M * embed<4>(light_dir)).normalize(); // 光线方向
        Vec3f r = (n * (n * l * 2.f) - l).normalize();   // reflected light
        float spec = pow(std::max(r.z, 0.0f), model->specular(uv));
        float diff = std::max(0.f, n * l);
        TGAColor c = model->diffuse(uv);
        for (int i = 0; i < 3; i++) color[i] = std::min<float>(20 + c[i] * shadow * (1.2 * diff + .6 * spec), 255);
        return false;
    }
};
```

渲染过程

```c++
DepthShader depthShader;
TGAImage depth(width, height, TGAImage::RGB);
viewTrans(light_dir, lookAt, up); // 注意 光源视角
viewport(width / 8, height / 8, width * 3 / 4, height * 3 / 4);
projection(0);

for (int i = 0; i < model->nfaces(); i++) {
    Vec4f screen_coords[3];
    for (int j = 0; j < 3; j++) {
        screen_coords[j] = depthShader.vertex(i, j);
    }
    triangle(screen_coords, depthShader, image, shadowbuffer);
}
depth.flip_vertically();
depth.write_tga_file("depth.tga");

 Matrix M = Viewport * Projection * ViewTrans;

TGAImage frame(width, height, TGAImage::RGB);
viewTrans(eye, lookAt, up);
viewport(width / 8, height / 8, width * 3 / 4, height * 3 / 4);
projection(-1.f / (eye - lookAt).norm());

SecondShader secondShader(ViewTrans, (Projection * ViewTrans).invert_transpose(), M * (Viewport * Projection * ViewTrans).invert());
Vec4f screen_coords[3];
for (int i = 0; i < model->nfaces(); i++) {
    for (int j = 0; j < 3; j++) {
        screen_coords[j] = secondShader.vertex(i, j);
    }
    triangle(screen_coords, secondShader, frame, zbuffer);
}
frame.flip_vertically();
frame.write_tga_file("shadowoutput.tga");
```

关于Z-fighting：
Z-fighting也被称为stitching或planefighting，是3D渲染中的一种现象，当两个或更多的图形元素（primitives）对相机的距离非常接近时就会发生。这会导致它们在Z缓冲区（z-buffer）中有非常接近或相同的值，Z缓冲区用于跟踪深度。

当渲染特定像素时，由于Z缓冲区无法精确区分哪一个元素离相机更远，因此不清楚应该绘制哪一个元素。如果一个像素无疑地更接近，那么就可以丢弃较远的那个。这种现象在共面多边形（coplanar polygons）中尤为常见，其中两个面占据了基本相同的空间，而没有一个在前面。

受影响的像素被一个或另一个多边形以一种由Z缓冲区的精度决定的方式随意渲染，这会导致两个多边形“争夺”着色屏幕像素。整体效果是两个多边形的闪烁，噪声光栅化。

这个问题通常是由于有限的子像素精度和浮点和定点舍入误差引起的。使用的Z缓冲区精度越高，遇到Z-fighting的可能性就越小。但对于共面多边形，除非采取纠正措施，否则问题是不可避免的。

Z-fighting可以通过使用更高分辨率的深度缓冲区，通过在某些场景中进行Z缓冲，或者简单地将多边形移得更远来减少。无法完全通过这种方式消除的Z-fighting通常通过使用模板缓冲区来解决，或者通过对一个多边形应用后变换屏幕空间Z缓冲区偏移，这不会影响屏幕上的投影形状，但会影响Z缓冲区值，以消除在像素插值和比较过程中的重叠