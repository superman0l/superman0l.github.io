## 大坑之有限元

#### Linear Finite Element Method (FEM)

![image](IMAGE/4-1.PNG)

$\bold F$表示相对位置变化，$c$表示三角形质心变化，该项计算中将三角形质心平移项去除了，但没有去除旋转项

![image](IMAGE/4-2.PNG)

根据转置消去 Rotation $\bold R(\bold U)$，Green strain表示形变拉伸的量

##### Strain Energy Density Function

能量->力->Hessian

![image](IMAGE/4-3.PNG)

作业使用模型：StVK, trace(G),矩阵G的迹，S为能量密度对形变拉伸量求导结果
$$
\bold f_i = -(\frac{\part E}{\part \bold x_i})^T=-A^{ref}(\frac{\part W}{\part \bold x_i})^T=
-A^{ref}(\frac{\part W}{\part \epsilon_{uu}}\frac{\part \epsilon_{uu}}{\part \bold x_i}+\frac{\part W}{\part \epsilon_{vv}}\frac{\part \epsilon_{vv}}{\part \bold x_i}+\frac{\part W}{\part \epsilon_{uv}}\frac{\part \epsilon_{uv}}{\part \bold x_i})^T
$$
$\frac{\part W}{\part \epsilon_{uu}}$ 等已知，求 $\frac{\part \epsilon_{uu}}{\part \bold x_i}$, 根据先前计算的$G = \frac{1}{2}(F^TF-I)$求导得到

<img src="IMAGE/4-4.PNG" alt="image" style="zoom: 50%;" /> 矩阵 很奇妙吧
$$
\bold f_1 = -A^{ref}FS\begin{bmatrix}a\\b\end{bmatrix}\\
\bold f_2 = -A^{ref}FS\begin{bmatrix}c\\d\end{bmatrix}\\
\bold f_0 = -\bold f_1-\bold f_2
$$

#### The Finite Volume Method (FVM)

![image](IMAGE/4-5.PNG)

![IMAGE](IMAGE/4-6.PNG)

Different Stresses, 取决于N和traction在形变状态前还是形变状态后

![image](IMAGE/4-7.PNG)

![image](IMAGE/4-8.PNG)

虽然但是 计算上并没有用这个推导的公式 麻了都

![image](IMAGE/4-9.PNG)
$$
[\bold b_1\ \bold b_2\ \bold b_3] = 6Vol[\bold X_{10}\ \bold X_{20} \ \bold X_{30}]^{T}\\
= \frac{1}{det([\bold X_{10}\ \bold X_{20} \ \bold X_{30}]^{-1})}[\bold X_{10}\ \bold X_{20} \ \bold X_{30}]^{T}
$$
![image](IMAGE/4-10.PNG)

#### Hyperelastic Models

**更加通用**的模型——Hyperelastic models

###### 只考虑各向同性材料Isotropic Materials

$$
\bold P(\bold F) = \bold P(\bold U\bold D \bold V^T) = \bold U \bold P(\lambda_0, \lambda_1, \lambda_2) \bold V^T\\
I=\lambda_0^2+\lambda_1^2+\lambda_2^2,II=\lambda_0^2\lambda_1^2+\lambda_0^2\lambda_2^2+\lambda_1^2\lambda_2^2,III=\lambda_0^4+\lambda_1^4+\lambda_2^4
$$

<img src="IMAGE/4-12.PNG" alt="IMAGE" style="zoom:67%;" />

<img src="IMAGE/4-11.PNG" alt="image" style="zoom:50%;" />

![IMAGE](IMAGE/4-13.PNG)

##### Summary

- FEM uses the derivates of the strain energy function to obtain the force
- FVM uses the integral of the interface traction to obtain the force
- Hyperelastic models define the strain energy function by principal stretches

#### 非线性优化

![image](IMAGE/4-14.PNG)

![IMAGE](IMAGE/4-15.PNG)

![IMAGE](IMAGE/4-16.PNG)
