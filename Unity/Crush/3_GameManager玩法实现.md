`GameManeger.cs`中，考虑的即核心玩法的实现，包括元素交换，遍历匹配，重新生成。

考虑游戏流程

开始游戏->检测匹配->消除->下落->新元素生成->检测匹配->消除------->玩家操作->检测匹配->....

因此在`GameManager.cs`中要实现的包括玩家操作、匹配、消除、下落、生成

##### 变量

```c#
public static GameManager Instance { get; private set; }//单例

[SerializeField] private Transform background;
[SerializeField] private Transform cubeBackground;
[SerializeField] private Transform crushItem;

private Transform[,] crushItemTable;//保存所有基本元素，由数组排列索引
private Transform selectedCrushItem;
private HashSet<CrushItem> matchItems = new HashSet<CrushItem>();//匹配元素的集合

private float minMoveDis = 0.3f;//鼠标移动的最短距离
```

##### 初始生成

```c#
private void Start(){
    SpawnCubeBackground();
    SpawnCrushItems();
    StartCoroutine(AutoMatchAgain());//一开始生成之后先进行一次匹配
}
//生成背景
private void SpawnCubeBackground(){
    for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT; ++rowIndex){
        for (int columnIndex = 0; columnIndex < GlobalDef.COLUMN_COUNT; ++columnIndex){
            var obj = Instantiate(cubeBackground);
            obj.transform.SetParent(background);
            obj.transform.localPosition = GetRowColPos(rowIndex, columnIndex);
        }
    }
}
private void SpawnCrushItems(){
    crushItemTable = new Transform[GlobalDef.ROW_COUNT, GlobalDef.COLUMN_COUNT];
    for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT; ++rowIndex){
        for (int columnIndex = 0; columnIndex < GlobalDef.COLUMN_COUNT; ++columnIndex){
            var obj = Instantiate(crushItem);
            obj.transform.GetComponent<CrushItem>().SpawnRandomCrushItem();
            obj.transform.position = GetRowColPos(rowIndex, columnIndex) + background.position;
            crushItemTable[rowIndex, columnIndex] = obj.transform;
            obj.GetComponent<CrushItem>().row = rowIndex; obj.GetComponent<CrushItem>().col = columnIndex;
        }
    }
}
```

##### 辅助函数

```c#
private Vector3 GetRowColPos(int row, int col)//根据行列获取相对背景的坐标，世界坐标需要加上background.position
{
    float item_x = (col - GlobalDef.COLUMN_COUNT / 2f) * GlobalDef.CELL_SIZE + GlobalDef.CELL_SIZE / 2f;
    float item_y = (row - GlobalDef.ROW_COUNT / 2f) * GlobalDef.CELL_SIZE + GlobalDef.CELL_SIZE / 2f;
    return new Vector3(item_x, item_y, 0);
}
private Transform GetItem(int row, int col){
    if (row < 0 || row >= GlobalDef.ROW_COUNT
    || col < 0 || col >= GlobalDef.COLUMN_COUNT) return null;
    return crushItemTable[row, col];
}
```

##### 元素选中

```c#
private void Awake(){
    Instance = this;
    EventDispatcher.instance.Regist(EventDef.EVENT_ITEM_SELECTED, SelectCrushItem);
}

private void OnDestroy(){
	EventDispatcher.instance.Unregist(EventDef.EVENT_ITEM_SELECTED, SelectCrushItem);
}

private void SelectCrushItem(params object[] objs){
    if((null == selectedCrushItem || selectedCrushItem != (objs[0] as CrushItem).transform) && (bool)objs[1])//CrushItem中传入的参数
    	selectedCrushItem = (objs[0] as CrushItem).transform;
    else
    	selectedCrushItem = null;
}
```

##### 元素交换

```c#
private void Update(){
    if (null == selectedCrushItem) return;
    if(Input.GetMouseButton(0)){
    	moveX = Input.GetAxis("Mouse X");
    	moveY = Input.GetAxis("Mouse Y");
    }

    if (Mathf.Abs(moveX) > minMoveDis || Mathf.Abs(moveY) > minMoveDis){
    	OnItemMove();
    }
    moveX = 0;moveY = 0;
}
private void OnItemMove(){
    var targetItem = GetTargetItem();
    if (null != targetItem){
        StartCoroutine(ExchangeAndMatch(selectedCrushItem, targetItem));
    }
    selectedCrushItem = null;
}
//相关该交换的都交换了，数组位置 物理位置 脚本中属性
private void Exchange(Transform item1, Transform item2){
    //位置交换
    var tempPositon = item1.position;
    item1.position = item2.position;
    item2.position = tempPositon;
    //属性交换
    CrushItem item1CrushItem = item1.GetComponent<CrushItem>();
    CrushItem item2CrushItem = item2.GetComponent<CrushItem>();
    int tmpRow = item1CrushItem.row, tmpCol = item1CrushItem.col;
    item1CrushItem.row = item2CrushItem.row;item1CrushItem.col = item2CrushItem.col;
    item2CrushItem.row = tmpRow;item2CrushItem.col = tmpCol;
    //table交换
    crushItemTable[item1CrushItem.row, item1CrushItem.col] = item1;
    crushItemTable[item2CrushItem.row, item2CrushItem.col] = item2;
}
```

##### 元素匹配

```c#
private bool CheckMatch(){
	bool isMatch = false;
	for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT; ++rowIndex){
    	for (int columIndex = 0; columIndex < GlobalDef.COLUMN_COUNT - 2; ++columIndex){//行匹配
        	var itemCrushItem = GetItem(rowIndex, columIndex)?.GetComponent<CrushItem>();
        	var itemCrushItem2 = GetItem(rowIndex, columIndex + 1)?.GetComponent<CrushItem>();
        	var itemCrushItem3 = GetItem(rowIndex, columIndex + 2)?.GetComponent<CrushItem>();
            if(itemCrushItem.GetSpawnedId() == itemCrushItem2.GetSpawnedId() &&
                    itemCrushItem.GetSpawnedId() == itemCrushItem3.GetSpawnedId()){
                isMatch = true;
                matchItems.Add(itemCrushItem);
                matchItems.Add(itemCrushItem2);
                matchItems.Add(itemCrushItem3);
            }
        }
    }
    for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT - 2; ++rowIndex){
        for (int columIndex = 0; columIndex < GlobalDef.COLUMN_COUNT; ++columIndex){//列匹配
            var itemCrushItem = GetItem(rowIndex, columIndex)?.GetComponent<CrushItem>();
            var itemCrushItem2 = GetItem(rowIndex + 1, columIndex)?.GetComponent<CrushItem>();
            var itemCrushItem3 = GetItem(rowIndex + 2, columIndex)?.GetComponent<CrushItem>();
            if (itemCrushItem.GetSpawnedId() == itemCrushItem2.GetSpawnedId() &&
                    itemCrushItem.GetSpawnedId() == itemCrushItem3.GetSpawnedId()){
                isMatch = true;
                matchItems.Add(itemCrushItem);
                matchItems.Add(itemCrushItem2);
                matchItems.Add(itemCrushItem3);
            }
        }
    }
    return isMatch;
}
//优化后
private bool CheckMatch(){
    bool isMatch = false;
    for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT; ++rowIndex){
        int count = 1;
        for (int columIndex = 1; columIndex < GlobalDef.COLUMN_COUNT; ++columIndex){
            //行匹配
            var itemCrushItem = GetItem(rowIndex, columIndex)?.GetComponent<CrushItem>();
            var itemCrushItem2 = GetItem(rowIndex, columIndex - 1)?.GetComponent<CrushItem>();
            if(itemCrushItem.GetSpawnedId() == itemCrushItem2.GetSpawnedId()){
                count++;
            }
            else{
                if (count >= 3){
                    while (count--)
                        matchItems.Add(GetItem(rowIndex, columIndex - count)?.GetComponent<CrushItem>());
                    isMatch = true;
                }
                count = 1;
            }
        }
        if(count >= 3){
            while (count--)
                matchItems.Add(GetItem(rowIndex, columIndex - count)?.GetComponent<CrushItem>());
            isMatch = true;
        }
    }
    for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT - 2; ++rowIndex){
        int count = 1;
        for (int columIndex = 0; columIndex < GlobalDef.COLUMN_COUNT; ++columIndex){
            //列匹配
            var itemCrushItem = GetItem(rowIndex, columIndex)?.GetComponent<CrushItem>();
            var itemCrushItem2 = GetItem(rowIndex - 1, columIndex)?.GetComponent<CrushItem>();
            if (itemCrushItem.GetSpawnedId() == itemCrushItem2.GetSpawnedId()){
                count++;
            }
            else{
                if (count >= 3){
                    while (count--)
                        matchItems.Add(GetItem(rowIndex - count, columIndex)?.GetComponent<CrushItem>());
                    isMatch = true;
                }
                count = 1;
            }
        }
        if (count >= 3){
            while (count--)
                matchItems.Add(GetItem(rowIndex - count, columIndex)?.GetComponent<CrushItem>());
            isMatch = true;
        }
    }
    return isMatch;
}
```

执行上述函数后，所有匹配的基础元素的脚本被添加到HashSet中

##### 元素消除和元素重新生成

```c#
IEnumerator ExchangeAndMatch(Transform item1, Transform item2){//使用线程 可以实现时间延迟，使玩家理解游戏操作效果
    // 交换
    Exchange(item1, item2);
    yield return new WaitForSeconds(0.3f);//时间延迟
    // TODO 检测是否可消除
    if (CheckMatch()){
        RemoveMatchItem();
        yield return new WaitForSeconds(0.6f);
        DropDown();//掉落
        matchItems.Clear();
        yield return new WaitForSeconds(0.6f);
        // 再次执行检测
        StartCoroutine(AutoMatchAgain());
    }
    else{//不能消除 换回来
        Exchange(item1, item2);
    }
    yield return null;
}
private void RemoveMatchItem(){
    foreach (var item in matchItems){
        item.DestroySpawned();
    }
}
//处理下落，遍历所有消除元素的上方元素，改变上方元素位置状态，之后将消除的元素重新利用
//这函数结构还能优化 
private void DropDown(){
    foreach (var item in matchItems){
        for (int j = item.row + 1; j < GlobalDef.ROW_COUNT; ++j){
            var tmpItem = GetItem(j, item.col)?.GetComponent<CrushItem>();
            tmpItem.row--;
            tmpItem.transform.position = GetRowColPos(tmpItem.row, tmpItem.col) + background.position;
            crushItemTable[tmpItem.row, tmpItem.col] = tmpItem.transform;
        }
        ReuseRemovedItem(item);
    }
}
//重新生成的该元素，重新生成RandomItem，将位置设置在该列的最上方，实现旧元素下落新元素出现在上方
private void ReuseRemovedItem(CrushItem item){
    if (null == item) return;
    item.SpawnRandomCrushItem();
    item.row = GlobalDef.ROW_COUNT - 1;
    item.transform.position = GetRowColPos(item.row, item.col) + background.position;
    crushItemTable[item.row, item.col] = item.transform;
}
```

##### 递归消除

```c#
IEnumerator AutoMatchAgain(){
    if (CheckMatch()){
        RemoveMatchItem();
        yield return new WaitForSeconds(0.2f);
        DropDown();

        matchItems.Clear();

        yield return new WaitForSeconds(0.6f);
        // 递归调用
        StartCoroutine(AutoMatchAgain());
    }
}
```

##### 检验可解性

```c#
private bool ChechCanCrush()
{
    //模拟所有交换情况后检测三消
    for (int rowIndex = 0; rowIndex < GlobalDef.ROW_COUNT; ++rowIndex){
        for (int columIndex = 0; columIndex < GlobalDef.COLUMN_COUNT; ++columIndex){
            var tmpItem = GetItem(rowIndex, columIndex);
            var tmpItemRight = GetItem(rowIndex, columIndex + 1);
            if (null != tmpItemRight){
                Exchange(tmpItem, tmpItemRight);
                if (CheckMatch()){
                    Exchange(tmpItem, tmpItemRight);
                    return true;
                }
                Exchange(tmpItem, tmpItemRight);
            }
            var tmpItemDown = GetItem(rowIndex - 1, columIndex);
            if (null != tmpItemDown){
                Exchange(tmpItem, tmpItemDown);
                if (CheckMatch()){
                    Exchange(tmpItem, tmpItemDown);
                    return true;
                }
                Exchange(tmpItem, tmpItemDown);
            }
        }
    }
    return false;
}
```

