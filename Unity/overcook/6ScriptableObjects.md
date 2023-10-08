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

这个就是各种食物的数据结构类型

```c#
//CuttingObjectSO.cs
[CreateAssetMenu()]
public class CuttingObjectSO : ScriptableObject
{
    public KitchenObjectSO input;
    public KitchenObjectSO output;
    public int CuttingCount;
}
```

```c#
//FryingObjectSO.cs
[CreateAssetMenu()]
public class FryingObjectSO : ScriptableObject{
    public KitchenObjectSO input;
    public KitchenObjectSO output;
    public int FryingTime;
}
```

```c#
[CreateAssetMenu()]
public class RecipeSO : ScriptableObject
{
    public List<KitchenObjectSO> kitchenObjectSOList;
    public string recipeName;
}
```

```c#
[CreateAssetMenu()]
public class RecipeListSO : ScriptableObject{
    public List<RecipeSO> RecipeSOList;
}
```

之后在具体各处用到的ScriptableObjects会具体说明 此处不再列举
