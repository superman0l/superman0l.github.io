### Android的UI布局

##### 文本组件TextView

```xml
<TextView
          android:id＝"@+id/textview"
          android:layout_width="match_parent" 
          android:layout_height="wrap_content"
          android:text="Hello World"
          android:gravity= "center" 
     上例属性居中 可以是"center_vertival|center_horizontal"
          android:background＝"#00F"
          android:textSize="30sp"
          android:textColor="#FF0"/>
```

##### 按钮Button

```xml
<Button
          android:id＝"@+id/button"
          android:layout_width="match_parent" 
          android:layout_height="wrap_content"
          android:text="Button"/>
```

当onClick方法的代码很多时，需要将onClick方法的代码从onCreate方法中移出来，因此要让MainActivity类实现View.OnClickListener接口然后将参数设置this

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState) 
    setContentView(R.layout.activity_main) 
    var button= findViewByid<Button>(R.id.button) // 已废弃
    button.setOnClickListener(this)
}
override fun onClick(view : View?){
    ...
}
```

多个按钮可以公用一个onClick，假设存在三个button的id分别为button、button1和button2

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    button.setOnClickListener(this)
    button1.setOnClickListener(this)
    button2.setOnClickListener(this)
}
override fun onClick(view: View?) {
    if(view is Button) { 
        // 通过 android: id 属性值判断单击的是哪一个按钮
        when(view.id) {
            R.id.button->
            	...
            R.id.buttonl-> 
                ...
            R.id.button2-> 
                ...
        }
    }
}
```

##### 文本编辑组件EditText

```xml
<EditText
          android:id＝"@+id/edittext"
          android:layout_width="match_parent" 
          android:layout_height="wrap_content"
          android:text="Hello World"
          android:hint="plaese input"
          android:maxLines="3" 最多显示三行 否则滚动
          />
```

##### 图像组件ImageView

需要先将图片放在drawable开头的目录中，例如yhk.jpg

```xml
<ImageView
          android:id＝"@+id/imageview"
          android:layout_width="wrap_content" 
          android:layout_height="wrap_content"
          android:sec="@drawable/yhk"
          />
```

点击按钮显示新图像

```kotlin
button.setOnClickListener(){
    imageview.setImageResource(R.drawable.smallapp)
}
```

##### 进度条组件ProgressBar

以下为圆形进度条

```xml
<ProgressBar 
          android:id＝"@+id/progressbar"
          android:layout_width="match_parent" 
          android:layout_height="wrap_content"/>
```

组件的 android:visibility 属性 该属性任何可视化组件都有。可选的值有：visible invisible gone 。

visible 表示组件是可见的，这个是默认值；invisible 表示组件不可见，但该组件仍然占据原来的大小， 可以理解成组件变成了透明状态。 gone 则表示组件不仅不可见，而且不再占用任何屏幕空间。

还可以通过代码来设置空间的可见性，使用的是 setVisibility 方法，可以 传入 View.VISIBLE、View.INVISIBLE 和View.GONE 。

方形进度条：

```xml
<ProgressBar 
          android:id＝"@+id/progressbar"
          android:layout_width="match_parent" 
          android:layout_height="wrap_content"
          style="?android:attr/progressBarStyleHorizontal"
          android:max="100"
             />
```

实现功能：单击按钮时进度不断加10直到100，再按从头开始

```kotlin
button.setOnClickListener(){
    // 获取进度条的当前进度
    var progress = progressbar.progress
    // 进度加 10
    progress+=10
    // 如果进度大子最大值
    if(progress > progressbar.max) 
    	progress = 0 
    // 重新设置当前进度
    progressbar.progress = progress
}
```

##### 对话框组件AlertDialog

```kotlin
button.setOnClickListener(){ 
    //创建 Builder 对象
	var dialog= AlertDialog.Builder(this) 
	dialog.setTitle("删除")
	dialog.setMessage("确认要删除数据吗")
	dialog.setCancelable(false) 
	// 设置确定按钮及单击确定按钮的事件方法 
    dialog.setPositiveButton("确定"){
		dialog, which -> 
			Toast.makeText(this,"点击确认按钮", Toast.LENGTH_LONG).show() 
    }  
    // 设置取消按钮及单击取消按钮的事件方法
	dialog.setNegativeButton("取消"){
		dialog,which -> 
			Toast.makeText(this,"点击取消按钮", Toast.LENGTH_LONG).show() 
    }
	dialog.show () // 显示对话框
}
```

#### 布局

##### 线性布局LinearLayout

线性布局允许其中的组件在水平或垂直方向顺序线性排列 。通过 android: orientation 属性，可以设置线性布局的方 向，该属性可 以取两个值： vertical 和horizontal 

android:layout_gravity 指定当前组件内容相对于父组件的对齐方式

`android:layout_gravity: "top"`或`"center_vertical"`或`"bottom"`

android:layout_weight 这个属性允许我们使用比例的方式指定组件的大小

```xml
<EditText 
android:layout width="match_parent" 
android:layout_height="0dp"
android:layout_weight="1" 
android:hint ="请输入标题"
/>
<EditText 
android:layout_width="match_parent" 
android:layout_height="0dp" 
android:layout_weight="3"
android:hint="输入要发送的"
/>
<Button 
android:layout_width="match_parent" 
android:layout_height="wrap_content" 
android:text ="点击发送"
/>
```

两个 EditText 组件中的 android: layout_ weight 属性值分别为1和3 ，而 android: layout_ height 属性的值都为 0dp ，这就意味着系统不会考虑用 android:layout_height属性设置这两个 EditText 组件的高度，转而使用 android: layout_ weight 属性按比例设置两个 EditText 组件的高度。

Button 组件的高度设为 wrap_content ，这就意味 Button 组件会根据自身的内容设置高度 android:layout_weight 属性的规则是先排列高度是 wrap_content 的组件，然后把剩下的空间都给设置了 android: layout_weight 属性的组件，并按比例排列这些组件。

##### 相对布局RelativeLayout

RelativeLayout 采用了相对位置进行排列，可以相对于父组件，也可以相对于同层次的 其他组件。

相对于父组件布局

```xml
<Button 
        ...
    android:layout_alignParentLeft="true" 
    android:layout_alignParentTop="true"
        />
```

相对于同层次组件布局

```xml
<Button 
    android:id="@+id/button3"
    ...
    android:layout_centerinParent="true" 
    />
<Button 
    ...
    android:layout_above="@+id/button3"
    android:layout_toLeftOf="@+id/button3"
    />
<Button 
    ...
    android:layout_above="@+id/button3"
    android:layout_toRightOf="@+id/button3"
        />
```

##### 帧布局FrameLayout

层叠布局，会直接叠起来（没有什么特别的属性，不写了

##### 百分比布局PercentFrameLayout

在这种布局中允许直接指定组件所占的百分比

不属于标准android布局需要在工程中添加依赖：

打开app/build.gradle文件，dependencies部分添加百分比布局依赖：

```
dependencies{
	...
	compile 'com.android.support:percent:24.2.1'
}
```

2个按钮各占屏幕1/4：

```xml
<android.support.percent.PercentFrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
<Button 
    ...
    android: layout_gravity="left|top"
    app:layout_widthPercent="50%"
    app:layout_heightPercent="50%"
    />
<Button 
        ...
        android:layout_gravity="right|top" 
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
            />
</android.support.percent.PercentFrameLayout>
```

