Scriptable Object可以很方便地定义一个类的多种不同实例，如多种武器、多种装备、多种食物等等

创建KitchenObjectSO.cs

```c#
[CreateAssetMenu()]
public class KitchenObjectSO : ScriptableObject{
    public Transform prefab;
    public Sprite sprite;
    public string objectName;
}
```

