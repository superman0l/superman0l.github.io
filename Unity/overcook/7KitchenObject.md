### KitchenObject

因为KitchenObject还是相对独立的 所以还是专门一栏讲

对于一般物体的KitchenObject操作基本一致

对于KitchenObjectSO而言,prefab存储预制件Transform,sprite存储贴图用于显示UI,name名字用于辨认

对于预制件KitchenObject,一般的格式是在空物体中搭载`KitchenObject.cs`,再在子对象中把KitchenObjectVisual放进去,KitchenObject一般没有额外动画效果,因此不需要`KitchenObjectVisual.cs`

```c#
//KitchenObject.cs
public class KitchenObject : MonoBehaviour{
    [SerializeField] private KitchenObjectSO kitchenObjectSO;
    //在prefab中搭载对应KitchenObject的KitchenObjectSO
    private IKitchenObjectParent kitchenObjectParent;

    public KitchenObjectSO getKitchenObjectSO(){
        return kitchenObjectSO;
    }

    public static KitchenObject SpawnKitchenObject(KitchenObjectSO kitchenObjectSO, IKitchenObjectParent kitchenObjectParent){
        Transform kitchenObjectTransform = Instantiate(kitchenObjectSO.prefab);
        KitchenObject kitchenObject = kitchenObjectTransform.GetComponent<KitchenObject>();
        kitchenObject.setKitchenObjectParent(kitchenObjectParent);
        return kitchenObject;
    }

    public void setKitchenObjectParent(IKitchenObjectParent kitchenObjectParent){
        if (kitchenObjectParent.hasKitchenObject()) // 判断新父对象是否为空{
            Debug.LogError("kitchenObjectParent already has a KitchenObject!");
            return;
        }
        kitchenObjectParent.setKitchenObject(this);

        this.kitchenObjectParent?.clearKitchenObject();//解除当前父对象
        this.kitchenObjectParent = kitchenObjectParent;

        transform.parent = kitchenObjectParent.getKitchenObjectPos();
        transform.localPosition = Vector3.zero;
    }

    public IKitchenObjectParent getkitchenObjectParent(){
        return kitchenObjectParent;
    }

    public void DestroySelf(){
        getkitchenObjectParent().clearKitchenObject();
        Destroy(gameObject);
    }
}
```

然后其实是想在KitchenObject这一栏专门讲**Plate**

#### Plate 盘子

其他的物体基本不需要处理逻辑,如切菜和烘焙等逻辑交由柜台处理,但是盘子需要逻辑来处理盘子中装了什么内容,并且还要根据盘子中装的内容进行UI的显示来让玩家方便理解盘子中盛放了什么物体

创建`PlateKitchenObject.cs`

```
public class PlateKitchenObject : KitchenObject
{
    private List<KitchenObjectSO> kitchenObjectSOList;//盘子中此时盛放的物体列表
    //在inspector中规定盘子可搭载的物体列表
    [SerializeField] private List<KitchenObjectSO> enableKitchenObjectSOList;

    private void Awake(){
        kitchenObjectSOList = new List<KitchenObjectSO>();
    }
	//遍历可添加程序的列表
    public bool AddList(KitchenObjectSO kitchenObjectSO){
        foreach (var enableKitchenObjectSO in enableKitchenObjectSOList){
            if (kitchenObjectSO == enableKitchenObjectSO){
                foreach (var existedKitchenObjectSO in kitchenObjectSOList){
                    if (kitchenObjectSO == existedKitchenObjectSO)
                        return false;
                }
                kitchenObjectSOList.Add(kitchenObjectSO);
                return true;
            }
        }
        return false;
    }
}
```

