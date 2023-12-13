### FPS游戏开发基础

- FPS游戏简介
- FPS游戏基础框架
- weapon system
- 3c system
- 其他技术点

##### FPS游戏基础框架

weapon | 3c | Gameplay

————————————

Game System

————————————

Game Core | Third Party Library

————————————

Game Engine

例子：

WNGame：PVEGAME|PVPGAME|PAWN

WNEngine：LogicalGame|EventGame……

#### weapon system

程序逻辑分类：

- InsatantHit 既是命中
- Projecttile 投掷物

关键基础组件：

- 物理引擎：havok等等

  能做的：射线检测、轨迹计算等等

- 弹道：影响弹道的主要因素

  - 主视角角度
  - 后座
  - 连发数
  - 精准度
  - 散发度

  弹道模型：连发数和后坐力成线性由基准值达到最大值

  主弹道子弹射出方向 = 主视角角度 + 后坐力 + 精准度 + 散发度

  举例：散发方向 = 随机数 * 散发度 * 水平/垂直

- InstantHit Weapon开火流程

  射程内击中->距离衰减和穿透衰减->穿透/不穿透->根据连发数计算新的角度->下一次射击

WeaponFireComponent<-

- WeaponFireComponent_Instant<-
  - WeaponFireComponent_InstantSpread
- WeaponFireComponent_Melee
- WeaponFireComponent_Placing
- WeaponFireComponent_Projectile

WNEngine.WeaponStateMachine<-

- WNGameBase.WeaponStateMachine_C4Bomb
- WNGameBase.WeaponStateMachine_Grenade
- WNGameBase.WeaponStateMachine_M1014
- ……

#### 3C system(Character, Camera, Control)

##### Character技术要点

- 表现方面：模型+贴图+材质+动画+物理
- 逻辑、角色状态设计转换、细节问题

##### Camera技术要点

- 渲染顺序：枪械和手最后绘制（枪械靠墙穿模问题）
- FOV：角色和场景分开配置FOV（手持枪械不变但场景变化）
- 碰撞、位置控制：第三人称镜头距离
- 屏幕效果：闪光等

##### Control技术要点

- 设备和手感
- 适配问题
- 辅助瞄准
- 硬件

#### 其他技术点

##### 网络同步

帧同步和状态同步的区别 就不写了orz

###### 客户端预表现/服务器确认？

peek优势：先拉出来的人先看到架枪的人（位置更改状态同步需要时间）

###### 反外挂

- 定制
- 通用：内存修改、变速、抓包
- 辅助：模拟器、虚拟机
- 破解：脱机、破解客户端

应用层：游戏逻辑、游戏引擎；系统层；硬件层

手段：服务器校验、服务器数据下发、客户端数据加密、客户端动态检测

###### 性能优化

- CPU性能优化
  - 物理性能
  - 动画：Update LOD
  - 流程逻辑
  - Pool
- 渲染性能优化：视锥裁剪、距离裁剪、遮挡剔除
  - UV场景优化
  - 制作规范
- 内存优化
  - 资源压缩
  - 资源分包
- 流量优化
  - 合并网络包
- 游戏卡顿优化
  - 预加载
  - 分帧处理