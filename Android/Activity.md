



### Activity

Activity - 窗口

主要用于显示UI

###### 创建Activity

Activity类需要从AppCompatActivity继承，并且需要重写Activity类中的onCreate函数

```kotlin
class MyActivity : AppCompatActivity(){
    override fun onCreate(savedInstanceState: Bundle?){
        super.onCreate(savedInstanceState)//重写的函数必须调用父类中同样的函数
    }
}
```

onCreate是Activity初始化函数 是Activity的7个生命周期函数之一 主要用途关于创建和初始化组件以及其他资源

###### 创建和加载布局Layout

Android中布局使用XML文件格式描述，一个布局是一个XML文件，所有布局文件必须在res目录的layout目录中。

设计布局可以可视化和手工编写代码。

初次创建Layout：New -> Android resource file -> 选择Layout

在activity中使用该布局文件

```kotlin
super.onCreate(savedInstanceState)
setContentView(R.layout.my_activity)
```

按钮布局：

```xml
<Button
        android:id="@+id/button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="onClick" //这行
        android:text="Button" />
```

###### 在AndroidManifest文件中注册Activity

所有activity都需要注册 第一个启动的窗口需要intent-filter

```xml
<activity
          android:name=".MainActivity"
          android:exported="true"
          android:label="@string/app_name"
          android:theme="@style/Theme.A_pplication">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

##### 获取视图实例

该书中获取视图实例的方法是`findViewByid(R.id.editText)`但当前ViewBinding是google官方推荐的获取视图实例的方法（来自newbing

首先在`build.gradle`文件中启用

```groovy
android {
    ...
    buildFeatures {
        ...
        viewBinding = true
    }
}
```

```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding // 布局文件为activity_main.xml

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // 初始化binding 这很重要
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.editText.setText("你的文本")
    }
}
```

###### Toast

按钮显示Toast功能：

```kotlin
private lateinit var binding: MainActivityBinding // main_activity.xml
override fun onCreate(savedInstanceState: Bundle?) {
    ....
    binding.firstButton.setOnClickListener {
        // 在这里处理点击事件
        binding.editText.setText("Click")
        Toast.makeText(this, "Toast_Hint", Toast.LENGTH_LONG).show()
    }
}
```

Button 组件在默认情况下会将所有英文字母都变大写。 如果将 <Button>标签的`android:textAllCaps`属性值设为false就会按原样输出英文字母

###### 关闭Activity

```kotlin
binding.closeButton.setOnClickListener {
    // 在这里处理点击事件
    finish()
}
```

##### Intent连接多个Activity

创建新Activity：New -> Activity -> EmptyActivity

###### 显式跳转

```kotlin
binding.showButton.setOnClickListener {
    var intent = Intent(this, SecondActivity::class.java)
    startActivity(intent)
}
```

```kotlin
class SecondActivity : ComponentActivity() {...}
```

###### 隐式跳转

```xml
<activity
          android:name=".SecondActivity"
          android:exported="false" >
    <intent-filter>
        <action android:name="com.geekori.activity.SECOND_ACTIVITY"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```

```kotlin
binding.showButton2.setOnClickListener {
    var intent = Intent("com.geekori.activity.SECOND_ACTIVITY")
    startActivity(intent)
}
```

为了尽可能避免多个 Activity 使用同 Action 而造成的麻烦，可以在为 Activity Action 的同时，再指定 Category 。这样就相当于将两个字符串与同 Activity 绑定，只有都满足，当前 Activity 才会被选中

```xml
<action android:name="com.geekori.activity.SECOND_ACTIVITY"/>
<category android:name="android.intent.category.DEFAULT"/>
<category android:name="com.geekori.category.SECOND_ACTIVITY"/>
```

```kotlin
var intent = Intent("com.geekori.activity.SECOND_ACTIVITY")
intent.addCategory("com.geekori.category.SECOND_ACTIVITY")
startActivity(intent)
```

如果使用 action和 category 仍然不能很好地过滤 Activity ，也就是还有重复的 Activity ，可 以使用第3个过滤机制，这就是 Data

```xml
<action android:name="com.geekori.activity.SECOND_ACTIVITY"/>
<category android:name="android.intent.category.DEFAULT"/>
<category android:name="com.geekori.category.SECOND_ACTIVITY"/>
<data android:schemes="https" android:host="geekori.com"/>
```

```kotlin
var intent = Intent("com.geekori.activity.SECOND_ACTIVITY")
intent.addCategory("com.geekori.category.SECOND_ACTIVITY")
intent.setData(Uri.parse("https://geekori.com"))
startActivity(intent)
```

隐式跳转到web

```kotlin
binding.webButton.setOnClickListener {
    var intent= Intent(Intent.ACTION_VIEW)
    intent.setData(Uri.parse("https://geekori.com"))
    startActivity(intent)
}
```

> - android:scheme 用于指定 Uri 的协议部分，如 http https等。 
>
> - android:host 用于指定 Uri 主机名部分，如 geekori.com www.google.com 等。
>
> - android:port 用于指定 Uri 端口部分 端口会跟在主机名的后面，用冒号分隔， geekori.com:8080 中的 8080 )
>
> - android:path ：用 于指定主机名和端口之后的部分，如 https://geekori.com/blogsCenter.php?uid=geekori 中的 /blogsCenter ip?uid=geekori 都属于 Path 
>
> - android:mimeType 用于指定可以处理的数据类型，如 image/png application/pdf 等。 
>
>   如果这些属性都设置了，那么只有同时满足所有属性的值，＜data>标签的过滤条件才会成立，并且还要满足＜action＞和＜category＞标签的过滤条件，这样当前的 Activity 才会满足过滤条件。

##### Activity传递数据

```kotlin
binding.showButton.setOnClickListener {
    var intent = Intent(this, SecondActivity::class.java)
    intent.putExtra("name","Billy")
    intent.putExtra("age",18)
    startActivity(intent)
}
```

```kotlin
var name : String? = intent.getStringExtra("name")
var age : Int? = intent.getIntExtra("age" , 0)
name?.let{ receiveName->
          age?.let{receiveAge->
                   binding.textView.setText(receiveName+receiveAge.toString())
                  }
         }
```

##### Activity返回数据

`startActivityForResult()`方法已经被弃用,Android官方推荐使用`registerForActivityResult()`方法来替代`startActivityForResult()`。

这是因为`startActivityForResult()`方法在处理多个Activity结果时，可能会导致`onActivityResult`里掺杂各种嵌套及判断，使得代码难以维护.

```kotlin
private val launcherActivity = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    val code = result.resultCode
    val data = result.data
}

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    launcherActivity.launch(Intent(this, SecondActivity::class.java))
}
```

首先，`registerForActivityResult()`是一个方法，它接受一个`ActivityResultContract`和一个`ActivityResultCallback`作为参数。`ActivityResultContract`定义了你想要启动的Activity类型以及返回的数据类型。`ActivityResultCallback`是一个回调函数，当Activity返回结果时，这个函数会被调用。

在我的示例中，我使用了`ActivityResultContracts.StartActivityForResult()`作为`ActivityResultContract`。这是一个预定义的`ActivityResultContract`，它表示我想要启动一个Activity并获取其返回结果。

然后，我定义了一个`ActivityResultCallback`，它是一个lambda函数。这个函数接受一个`ActivityResult`作为参数。`ActivityResult`包含了返回的结果码和数据。

在这个回调函数中，你可以处理返回的结果。例如，你可以检查结果码是否表示成功，然后根据返回的数据更新UI

**示例：**

```kotlin
private val launcherActivity = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
   val code = result.resultCode
   val data = result.data
   val returnedResult = data?.getStringExtra("result")
   if(code == Activity.RESULT_OK) binding.editText.setText(returnedResult)
  }
```

```kotlin
binding.closeButton.setOnClickListener {
    // 创建 Intent 对象，该对象会被 SecondActivity 返回
    var intent= Intent()
    // 设置要返回的数据
    intent.putExtra("result","resultData")
    // Activity.RESULT_OK 是响应码， intent 是返回的 Intent 对象
    setResult (Activity.RESULT_OK, intent)
    finish()
}
```

##### Activity生命周期

- onCreate ：这个方法已经看到过很多次了，Activity 都需要重写这个方法，这 个方法在 Activity 次被创建时调用。 onCreate 方法中，完成对 Activity 初始化工作，如创建组件、设置成员变量的值等 
- onStart ：这个方法在 Activity 由不可见变为可见的时候调用 
- onResurne ：这个方法在 Activity 准备好与用户进行交互时调用。 、
- onPause ：这个方法在系统准备去启动或者恢复另 Activity 时调用 我们通常会 在该方法中将一些消耗 CPU 的资源释放，以及保存 些关键数据 
- onStop ：这个方法在 Activity 完全不可见时调用 如果 Activity被销毁，那么首先会 执行 onPause 接下来就会执行 onStop 
- onDestroy ：这个方法在 Activity 被销毁之前调用，之后 Activity 的状态就变为销毁状态了。 
- onRestart ：这个方法在 Activity 由停止状态变为运行状态之前调用，也就是 Activity 被重新启动了。

完整生存期： Activity 在 onCreate 方法和 onDestroy 方法之间所经历的，就是完整生存期。 一般情况下， Activity 会在 onCreate 方法中完成各种初始化操作， 而在onDestroy 方法中完成释放内存的操作。

可见生存期： Activity在 onStart 方法和 onStop 方法之间所经历的，就是可见生存期。 在可见生存期内， Activity 对于用户总是可见的，即便有可能无法与用户进行交互。 我们可以通过 onStart 和onStop 方法合理地对资源进行管理。例如，可以在 onStart 方法中装载资源，在 onStop 方法中释放资源，这样就可以保证在 Activity 于停止的状态下不会占用太多的资源。 

前台生存期： Activity在 onResume 方法和 onPause 方法之间所经历的 就是前台生 存期。在前台生存期内， Activity 总是处于运行状态，此时的 Activity 是可以和用户 进行交互的，我们平时看到和接触最多的也就是这个状态下的 Activity

##### 用BaseActivity基类来记录当前活动

```kotlin
open class BaseActivity : ComponentActivity(){
    override fun onCreate(savedinstanceState : Bundle? ) {
        super.onCreate(savedinstanceState)
        // 将当前类的名称输出到 Logcat
        Log.d("BaseActivity", javaClass.simpleName)
    }
}
```

##### 一些异常

`java.lang.RuntimeException: Unable to start activity ComponentInfo{com.example.a_pplication/com.example.a_pplication.SecondActivity}: java.lang.IllegalStateException: You need to use a Theme.AppCompat theme (or descendant) with this activity.`

如果用AppCompatActivity的话，需要把activity用的theme改了，如果activity中没指定theme用的就是在application中theme改成android:theme="@style/Theme.AppCompat.Light.DarkActionBar"

-------------------------

别随便动xml 可能会导致奇怪的看不见

`ComponentActivity`和`AppCompatActivity`都是Android中的Activity类，它们都用于创建应用程序的用户界面。下面是它们的主要区别：

- [`AppCompatActivity`是`FragmentActivity`和`ComponentActivity`的子类，它提供了对ActionBar的支持，这是一个顶部的应用程序栏，可以显示导航控件，品牌logo，标题等](https://www.cnblogs.com/Jeely/p/11063412.html)[1](https://www.cnblogs.com/Jeely/p/11063412.html)。如果你的应用需要使用到这些功能，或者需要兼容旧版本的Android，那么你应该使用`AppCompatActivity`。
- [`ComponentActivity`是一个更轻量级的Activity，它是Jetpack组件库中的一部分，用于支持现代Android开发的最佳实践](https://www.cnblogs.com/Jeely/p/11063412.html)[2](https://zhuanlan.zhihu.com/p/380003449)。如果你的应用不需要使用到ActionBar，或者你正在使用新的UI工具如Jetpack Compose，那么你可以选择使用`ComponentActivity`。

[总的来说，你应该根据你的应用的需求来选择使用哪一个Activity。如果你需要更多的兼容性和功能，那么`AppCompatActivity`可能是更好的选择。如果你希望你的应用更加轻量级，或者你正在使用最新的Android开发工具和最佳实践，那么`ComponentActivity`可能是更好的选择。](https://www.zhihu.com/question/609721802)[3](https://www.zhihu.com/question/609721802)
