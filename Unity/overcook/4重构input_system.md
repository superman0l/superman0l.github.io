重构的目的是单独调用一个脚本来处理外设的输入，角色的逻辑处理先由输入系统处理之后在反馈到角色的逻辑上

首先在Scene中创建一个空对象`GameInput`，用于挂载`GameInput`脚本

然后在`GameInput`脚本里处理输出并定义public函数用于外部脚本调用



然后再去Package Manager里找Input System

安装之后不着急点应用 先去调设置

在Project Setting里的Player 找到Other Settings -> Configuration -> Active Input Handling

做视频的说他喜欢两个输入系统一起用所以选both = = 

重启之后在Project左下角点击+号添加Input Actions

进入Input Actions页面，在Action Maps中添加一项命名为Player，Actions中命名为Move，Action Properties中设置Action Type为Value，设置Control Type为Vector 2，然后把默认的binding删掉，添加Up/Down/..的binding，对于添加的binding可以重命名为W A S D（？我不知道之后要不要考虑变更键位），然后对up down的绑定Path点listen用按键响应，然后对于Actions的Move设置Processors `Normalize Vector 2`这样位置输出就是单位Vector 2，之后在Player Input Actions 的inspector里apply生成C#脚本

在Input System脚本里更改输入的处理

```c#
private PlayerInputActions playerInputActions;

private void Awake(){
playerInputActions = new PlayerInputActions();
playerInputActions.Player.Enable();
}

public Vector2 getMoveVectorNormalized(){
Vector2 inputVector = playerInputActions.Player.Move.ReadValue<Vector2>();
return inputVector;
}
```

如果要支持新的移动方式，如手柄，可以在Player Input Actions中选择Edit asset继续进行编辑，然后在move中添加新的绑定，比如绑定上下左右箭头等等