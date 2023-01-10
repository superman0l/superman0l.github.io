# 一些关于Unity的东西
这里用来写一些建2D工程的经验
## 角色的创建
png图片拖到里面就好了

需要添加的组件：

Rigidbody（刚体，用于改变速度、作用力等）<br>
Collider（碰撞体）
对于角色所使用的Collider，建议使用Capsule collider（胶囊状碰撞体），因为使用tilemap创建地图碰撞箱时，Box collider容易因为一些缝隙导致角色的运动卡住<br>
Script（角色所需脚本，角色所要完成的操作的代码都在该脚本上编写）<br>
Animator（动画管理器，用于进行各个动画的衔接）<br>
暂时就用了这些<br>

### Script相关：

对于Script的类中变量的声明<br>
用public的声明可以使参数出现在unity的面板右侧，可以在游戏运行时通过观察右侧面板的数值进行debug，也可以手动更改，方便进行数值大小的调试<br>
用public static的声明可以让其他类的函数(其他脚本)对该变量进行更改，如不同的场景对角色产生的不同影响，表示影响的变量可以用public static声明<br>
直接声明类型的变量则是该类中直接私有的变量，一般用于函数内部计算<br>

对于游戏中对象的获取
`GameObject.Find(String "Object_name")`用来直接获取游戏对象
`GameObject.Find(String "Object_name").GetComponent<Rigidbody>`用来获取该游戏对象的刚体组件,以此类推

</p>
