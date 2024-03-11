```c++
#include "tgaimage.h"
const TGAColor white = TGAColor(255, 255, 255, 255);
const TGAColor red = TGAColor(255, 0, 0, 255);
int main(int argc, char** argv) {
    TGAImage image(100, 100, TGAImage::RGB);
    image.set(99, 99, red); // 设置右上角红点
    image.flip_vertically(); // 垂直翻转
    image.write_tga_file("output.tga");
    return 0;
}
```

