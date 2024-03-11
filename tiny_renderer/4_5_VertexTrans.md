向量处理

If a programmer writes vec2(x,y), is it a vector or a point? Hard to say. In homogeneous coordinates all things with z=0 are vectors, all the rest are points. Look: vector + vector = vector. Vector - vector = vector. Point + vector = point.



向量升维

```c++
Vec3f m2v(Matrix m) {
    return Vec3f(m[0][0] / m[3][0], m[1][0] / m[3][0], m[2][0] / m[3][0]);
}

Matrix v2m(Vec3f v) {
    Matrix m(4, 1);
    m[0][0] = v.x;
    m[1][0] = v.y;
    m[2][0] = v.z;
    m[3][0] = 1.f;
    return m;
}
```

