- 基本概念 常用组件

- 界面动起来
-  
-  内存
-  



### 

UE4提供的界面开发系统

- HUD

head up display 抬头显示 直接贴图？

- Slate

虚幻引擎自定义用户界面的名称 包括输入处理 事件分发 绘制 渲染

- UMG slate太弱智了所以来一个图形化界面来写

UMG是对Slate的封装，方便界面编辑以及支持虚幻的垃圾回收机制

### 基本概念 常用组件

控件Widget

引擎预先封装的功能单元：Image、Button

UI蓝图

蓝图代码 控制控件的显示隐藏 响应事件

Slot槽

用于容器类控件 用于摆放子空间 位置 大小 堆叠次序

锚点Anchor

子控件相对父控件的位置关系

对齐点：原点的位置

##### 可见性

- 折叠 直接不出现（逻辑空间也没有）
- 隐藏 还在那但是不给你看
- 不可点击 
- 完全显示

##### 渲染变形

对摆好的控件进行变形而不用更改布局信息，一般适用于不规则的形状或临时变形（控件动画）



- 按钮
- 复选框
- 图像
- 进度条
- 文本
- 文本框

画板控件

- 画布画板 Canvas Panel
- 网格面板 Grid Panel
- 水平框 Horizontal Box
- 纵向框 Vertical Box
- 滚动框 Scroll Box
- 控件切换器 Widget Switcher （tab）

优化控件

- 无效框

封装在里面的控件可以令子控件几何图形进行缓存，加快平板的渲染速度。（缓存无效框里的东西不用重新跑）

- 限位框

控制屏幕和相位 可以把UI的实际渲染频率低于游戏渲染频率

### 动起来

UMG创建动画

控件蓝图编辑器底部窗口 实施控制UI控件动画

第一个动画窗口创建动画基础

### 使用和控制编辑好的界面

显示界面 处理用户交互

控件变量：通过控件变量来操作控件设置属性

### 扩展控件库

自定义控件 避免重复造轮子

### 内存性能

使用图集

导入图片后uinterface压缩

extract

内存

资源引用形成、解隅

引用形成 直接摆放在UI的tree中

变量类型 创建UI时选择的类型

解隅：避免在蓝图中直接访问类型中方法，通过基类、接口调用

不要将各种使用的资源配置在UI上或蓝图里

弱引用 使用类似MVC 独立实现界面的数据、视图以及控制逻辑

性能

不用或少用属性绑定

尽量不在UI蓝图中实现Tick

复杂蓝图逻辑转CPP 在蓝图中调用CPP实现函数

正确设置隐藏(Hidden <==> Collapsed)

无效框缓存UI绘制中间数据，减少绘制的CPU消耗

合理结构提升渲染性能：

减少UI层级 从而减少UI绘制过程的递归调用 合理规划UI层次 合并图集 批量渲染以减少DrawCall