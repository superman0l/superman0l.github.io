- 简洁
- 变量名易理解
- 注意缩进
- 关键逻辑添加注释
- if-else添加注释方便看懂



UE4代码规范

命名

变量名大驼峰式，bool类型加b前缀

数据类型

- 基础uint64/int16
- 使用UE定义的FString/FText/FName/TCHAR
- 容器类不用stl，用TMap/TArray



辅助工具：Cpplint，Resharper C++等

| 开头 | 含义        | 示例                 |
| ---- | ----------- | -------------------- |
| T    | 模板类      | TMap                 |
| U    | 继承UObject | UMoviePlayerSettings |
| A    | 继承AActor  | APlayerCameraManager |
| S    | 继承SWidget | SCompoundWidget      |
| I    | 抽象接口类  | INavNodeInterface    |
| E    | 枚举        | EAccountType         |
| b    | 布尔变量    | bHasFadedIn          |
| F    | 其他类      | FVector              |

矩阵部分去听101 不想写