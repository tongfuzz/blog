<!--
title:Kotlin中的Lambda表达式内存泄漏问题
subtitle:Kotlin中的Lambda表达式内存泄漏问题
createDate:2022-11-16
updateDate:2022-11-16
tags:Kotlin,问题记录
imagePath:img/Kotlin中的Lambda表达式内存泄漏问题
-->


## `Kotlin` 中的 `Lambda` 表达式内存泄漏问题

`Kotlin` 中的 `Lambda` 表达式对应到 `Java` 中就是匿名内部类,匿名内部类持有外部类的应用，如果此匿名内部类被生命周期较长的对象持有，容易导致内存泄漏,示例如下：

```kotlin
class SplashActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_splash)
        LoadingTest.init(onloadfail).show()
    }

    //这种Lambda写法相当于java中的匿名内部类
    val onloadfail = {
        findViewById<TextView>(R.id.tv1).text = "time=" + System.currentTimeMillis()
    }
}
```

创建单例对象

```kotlin
object LoadingTest {

    private lateinit var onLoadFail: () -> Unit

    fun init(onLoadFail: () -> Unit = {}): LoadingTest {
        this.onLoadFail = onLoadFail
        return this
    }

    fun show() {
        onLoadFail.invoke()
    }
}
```

因为单例对象`LoadingTest`的生命周期与`app`的生命周期相同，由于单例对象持有`onLoadFail`匿名内部类对象，匿名内部类对象持有`Activity`,所以当`Activity`生命周期结束后其仍然不会被释放，造成内存泄漏
