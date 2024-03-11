基础术语

Player Controller: 玩家

Bot: 人机

NPC: 敌人



游戏AI: 村民、敌人、队友

AI目的：

- 代入感提升
- 强度适中挑战
- 行为合理 无明显破绽
- 需要真实

四大主题：

- 环境感应

  - 视野管理
  - 听觉管理

- 知识管理

  - Agent黑板

  - Squad黑板

    存储：消耗大的感应结果、寻路结果、plan结果

- 行为模型

  - 状态机  分层状态机 

  - 行为树：

    节点返回当前执行状态：运行中、成功、失败

    组合节点（composite）有1-n个孩子 决定直接孩子的执行方式：顺序、选择、并行、继承 扩展

    叶子节点：判断节点，读取环境信息判断是否符合条件 行为节点 执行具体行为

    节点可被继承为特定功能的节点类型

  - 效用决策：对环境特性加上权重，对于每类权重进行计算得到分数从而决定行为

    缺点：调参问题（数值策划狂喜

  - `GOAP`： Goal-Oriented Action Planners 目标导向行为规划

    开发者自定义每个行为：先决条件、消耗、结果

    从目标状态开始回溯搜索，搜索满足条件的行为列表，挑选消耗和最小行为列表执行

    只需配置行为，行为序列的建立通过运行时搜索动态建立

  - `HTN`： Hierachical Task Networks 层级任务网络

    元任务组合为组合任务，每个任务有自己的条件、行动、结果，运行时前向搜索，不断搜索符合条件任务，直到所有任务都是元任务，得出任务列表，执行

- 寻路

  - 原始地图
  - grid 量级大 搜素慢 平滑低
  - way_point 
  - 三角形离散 `Nav_Mesh`

  搜索：

  - A*

    open列表：挑出低消耗、高启发的节点作为当前搜索节点

    close列表：当前节点（搜索过）放入close，未处理过放入open

    直到open挑出目标节点

  - Jump Point搜索

    Jump Point不会直接将相邻加入Open列表，会跳步到**当前搜索方向临近有障碍物变化**的点



基础角色、控制器、行为树

行为树->AI-控制器>角色

- 创建敌人角色 复制`ThirdPersonCharacter`为新蓝图 重命名为Enemy拖到场景里

- 创建新蓝图继承自`AIController` 命名为`EnemyController`

- 创建敌人的行为树（AI-行为树） 命名`BT_Enemy`,在敌人AI控制器蓝图脚本开始时`EventBeginPlay`执行`BT_Enemy`（运行行为树）

- 添加Sequence节点和Wait节点 并运行能看到运行执行情况

- 环境->找到目标点->记住目标点->移动到目标点

- 创建AI寻路环境:NavMesh 

  - 将NavMeshBoundsVolume拖到场景 调整Scale
  - 自动构建 也可Build手动

- AI大脑记忆:黑板

  - 在行为树内创建使用的黑板BB_Enemy
  - 黑板中增加字段 Key名为PatrolLocation MoveTo节点

- 行为树TASK:修改黑板字段为随机位置,在行为树上使用Task

- 看到玩家并跟随:增加视觉->发现玩家->记住玩家->移动到玩家->忘记玩家

- 敌人控制器添加`AIPerception` 增加配置AI Sight config 并设为检测所有视力

- 玩家角色添加

- 在黑板增加用于视觉记忆的字段 TargetActor的Base Class

- 控制器增加视觉更新逻辑 当敌人控制器发现新的Actor进入视野 将其设定指定的字段

- 清除黑板特定字段

- 根据环境巡逻:可行走区域->判断环境(不贴墙移动 走直线)

- EQS Environment Query

  用EQS查询来代替随机获取位置(往前锥形范围检测 碰撞检测 和墙壁保持距离 距离检测 尽量远)

###### 实现总结

- AI Perception 和 EQS 敌人对AI环境感应
- 黑板 



https://github.com/recastnavigation/recastnavigation