# UE入门

课后作业要求：

- 根据上一节课的工程基础进行**迭代**开发
- 使用`github`，发送`github`链接
- 内容包括工程代码、资源、apk安装包、游戏运行的mp4视频（版本可提供移动版和桌面版，最好是移动版）
- 源码编写以第三节课代码规范为标准

###### 本节作业

- 源码编译UE，新建CPP工程，进行简单场景编辑和工程设置
- 编译并构建安装包，确保运行

## UE学习途径和方法的介绍

推荐用书：**Game Engine Architecture**

Game engine are data-driven architectures that are reusable and therefore do not contain game content

游戏引擎：渲染（GAMES101, GAMES202）

- Deferred Renderer
- Forward Renderer
- Mobile Renderer

游戏引擎：物理（碰撞检测，动态约束，刚体物理...）

- Havok
- PhysX
- Bullet

## UE编辑器使用和编程技巧

###### 游戏世界编辑器的典型功能

- 地图关卡的创建和分层
- 可视化游戏世界
- 导航
- 选取
- 属性设置
- 安放与对齐辅助工具
- 快速迭代
- Volume
- 光源

工程创建：左侧GAMES

布局就懒得说了 也不让放截图 注意UE5的UI布局很多东西都放在Sidebar

游戏地图切换：创建File -> New Level， Levels: NewMap, NewMap2

 View Mode切换： Lit/Unlit/Wireframe 有光照/无光照/线框

视角快捷操作：WASD+MouseRight 应该和Unity里一样 还有QE负责上升和下降 这点比Unity好多了

还可以调摄像机速度 便于快速过点（？

F：相机聚焦在所选物体上

Ctrl+数字：BookMark视点坐标（架设传送门）

G：Game View

Ctrl+点击：多选

World Outliner -> Search： 关键字/+完全匹配/-排除

Ctrl + Alt : 区域框选

W/E/R: transform/rotation/scale

世界坐标系和局部坐标系的切换

END: 中心贴地; Alt + End : 轴心贴地; Shift + End: 碰撞盒贴地;

Place Actors -> Basic -> Empty Pawn 

快速迭代： MODES -> Selected Viewport/Mobile Preview/New Editor Window...

Volumes and Lights: Place Actors

##### UE编辑器使用：

###### Content Browser 目录结构

- Content目录下文件结构与Windows资源管理器磁盘结构一一对应
- UE4资源为*.uasset文件
- 非UE4资源建议单独目录存放

###### Content Browser 资源迁移

- Asset Actions -> Migrate
- 使用资源迁移工具来保证内部资源之间的相互引用关系不丢失
- 资源必须在Content目录下

###### Derived Data Cache

- Separate source and derived data
- Different representation per platform
- Many advantages

###### Content Browser 命名规范

[链接1][link]  [链接2][link2]

[link]: https://github.com/Allar/ue4-style-guide
[link2]: https://github.com/skylens-inc/ue4-style-guide

- 目录结构 懒得上图之后再说
- 资源名称： `Prefix_BaseAssetName_Varient_Suffix`

###### 项目构成

- World 世界 包含载入的关卡列表
- Level 关卡 用户定义的游戏区域
- Actors 可放入关卡中的对象都是Actor，支持三维转换的泛型类，可通过游戏进程代码创建和销毁Actor
- Component 组件 是可添加到Actor的一项功能 不可独立存在 添加到Actor后Actor可访问

###### 工程目录结构

最外层目录

- /Engine
- /Samples
- /Templates

/Engine 和 /MyProject

- /Content
- /Source

###### 编译规则

目标文件

- 支持客户端、服务器、编译器等
- 通过C#源文件声明，扩展名.target.cs 存在Source目录

模块

- 通过C#声明 扩展名为.build.cs, 存在Source目录
- 属于一个模块的cpp代码与.build.cs文件并列存储

###### Entry Point

LaunchEngineLoop.cpp

 Q:在VS中显示完整宽度的工程设置

A:右键toolbar -> customize -> commands tab -> toolbar radio button -> standard -> solution configurations ->modify selection -> 200 -> close

Q: 在VS中显示Solution Platforms

A: Right-most button on the Standard toolbar -> Add or Remove Buttons ->Solution Platforms

- GameMode游戏模式 负责设置正在执行游戏的规则

- GameState游戏模式 包含赋值到游戏中每个客户端信息 整个游戏的游戏状态

- PlayerState玩家状态

- Pawn兵卒 Actor子类 充当生命体
  - Character角色 是 Pawn Actor的子类
- PlayerController玩家控制器

## UE引擎工具了解

Blueprint分类

- **Level Blueprint**
- **Blueprint Class**
- Data-Only Blueprint
- Blueprint Interface
- Blueprint Macros

##### Blueprint与CPP之间的相互函数调用

`UFUNCTION(BlueprintCallable)`

`UFUNCTION(BlueprintImplementableEvent)`

`UFUNCTION(BlueprintNativeEvent)`

###### Blueprint使用

- 蜘蛛网
- Diff和Merge不便

###### 使用原则

- 数值配置
- 简单效果展示
- 特别简单逻辑

##### Lua Plugin for UE

slua-unreal 

UnLua

- unreal引擎的插件
- 通过unreal反射能力导出蓝图接口和静态cpp接口给lua
- 支持lua到cpp/蓝图双向调用

##### 编码规范

- T 模板类前缀
- U 继承自UObject类前缀
- A 继承自AActor类前缀
- F structs等
- I 抽象接口类前缀
- E 枚举类型前缀
- b 布尔变量

##### 基本类型

不使用cpp原生变量，用ue(int32, uint32,...)

###### 局部关闭优化

`OptimizeCode = CodeOptimization.Never;`

#### 日志

Log:

- Channel
- Verbosity Level

可视化日志[链接][log]

[log]: https://www.unrealengine.com/zh-CN/blog/using-the-ue4-visual-logger

内置控制台 ：~键

###### 调试相机

命令freezerendering 冻结帧渲染

toggledebugcamera自由移动相机

show bounds可以显示没有被culling的物体包围盒

Q：以游戏模式启动和调试

- 从工程文件右键Launch game启动游戏
- Debug启动项加入-game参数可调试

Ctrl+Shift+， 在编辑器模式下截取GPU快照并可视化显示

RenderDoc独立图形调试工具 内置UE4

------------------------------------------------------------------------------------------------------------------------------

#### 作业1

- 登录EPIC，把EPIC账号和github账号绑定，然后邮箱会收到邮件邀请加入需要点确认，然后就可以访问页面
-  到ue的github页面之后选4.27的分支下载，然后开始等
- 下载完解压->Setup.bat->GenerateProjectFiles.bat(tip: 多线程下载:setup.bat --threads=20)
- 然后就有工程了，之后启动UE4.sln开始编译，生成UE4
- 编译完成后找到Editor.exe 启动
- 新建cpp项目 初始设置自己选 注意改名 默认名字中文打不开（妈的智障
- [安卓打包1][安卓1] [安卓打包2][安卓1]

[安卓1]: https://docs.unrealengine.com/4.27/zh-CN/SharingAndReleasing/Mobile/Android/Setup/AndroidStudio/
[安卓2]: https://zhuanlan.zhihu.com/p/610569845

踩过的坑：

- 找不到.NETFramework, Version=v4.6.2的... 

  解决方法：下载.NETFramework v4.6.2

- unable to find a valid installation of Visual Studio 

  解决方法：没安装相应的cpp组件，需要安装

  - 使用cpp的桌面开发
  - 使用cpp的游戏开发
  - 代码工具：NuGet包管理器

- C1076堆内存限制：

  `C:\Users\<user>\AppData\Roaming\Unreal Engine\UnrealBuildTool\BuildConfiguration.xml` 更改编译设置

  ```
  <?xml version="1.0" encoding="utf-8" ?>
  <Configuration xmlns="https://www.unrealengine.com/BuildConfiguration">
  <BuildConfiguration>
  	<bAllowXGE>false</bAllowXGE>
  </BuildConfiguration>
  <LocalExecutor>
  	<MaxProcessorCount>5</MaxProcessorCount>
  	<ProcessorCountMultiplier>2</ProcessorCountMultiplier>
  </LocalExecutor>
  <ParallelExecutor>
  	<MaxProcessorCount>5</MaxProcessorCount>
  	<ProcessorCountMultiplier>2</ProcessorCountMultiplier>
  	<bStopCompilationAfterErrors>true</bStopCompilationAfterErrors>
  </ParallelExecutor>
  <WindowsPlatform>
  	<PCHMemoryAllocationFactor>1500</PCHMemoryAllocationFactor>
  </WindowsPlatform>
  </Configuration>
  ```

- C4834 放弃具有"nodiscard"属性的函数的返回值

  屏蔽报错即可

- ……用“0”替换“#if/#elif” (../wrl/event.h示例情况)

  `HoloLensTargetPlatform`模块的问题，找到`HoloLensTargetPlatform.Build.cs`在最后加上`bEnableUndefinedIdentifierWarnings = false`，以此类推 哪里报错加哪里

- 安卓的坑基本是`sdk manager`的各个版本关系处理的不对，完全跟着链接的配置来搞应该没问题

