# 一些关于Unity的东西
这里用来写一些建2D工程的经验
## 角色的创建
png图片拖到里面就好了
需要添加的组件：
<br>Rigidbody（刚体，用于改变速度、作用力等）
<br>Collider（碰撞体）
对于角色所使用的Collider，建议使用Capsule collider（胶囊状碰撞体），因为使用tilemap创建地图碰撞箱时，Box collider容易因为一些缝隙导致角色的运动卡住
<br>Script（角色所需脚本，角色所要完成的操作的代码都在该脚本上编写）
<br>Animator（动画管理器，用于进行各个动画的衔接）
<br>暂时就用了这些
### Script相关：
对于Script的类中的声明
<br>用public的声明可以使参数在unity的面板右侧