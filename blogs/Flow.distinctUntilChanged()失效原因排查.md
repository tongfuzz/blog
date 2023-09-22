<!--
title:Flow.distinctUntilChanged()失效原因排查
subtitle:在使用kotlin flow的时候经常会使用到distinctUntilChanged方法来过滤相同的数据,可是在使用的过程中却发现并为生效,排查许久发现是定义数据类时将data class定义为了class，导致了此问题的发生
createDate:2023-03-02
updateDate:2023-03-02
tags:Kotlin,问题记录
imagePath:img/Flow.distinctUntilChanged()失效原因排查
-->

## Flow.distinctUntilChanged()失效原因排查

在使用 `flow` 的时候经常会使用到 `distinctUntilChanged` 来过滤相同的数据，特别是当 `Activity/Fragment` 等界面通过 `flow` 数据流来更新 `ui` 时，重复的数据会导致多次无意义的重复更新，我们可以使用 `distinctUntilChanged()`方法让其仅在数据发生改变时更新

#### 问题描述

在使用 `distinctUntilChanged()`来过滤数据时发现过滤失效，对比了接口返回的数据与本地缓存的数据，发现确实是相同的数据，排查许久发现是定义数据类时将 `data class` 定义为了 `class`，导致了此问题的发生

#### 问题重现

```kotlin
private fun distinctUntilChangedDemo() {
        class SizeInfo(val width: Int, val height: Int)
        data class Computer(val brand: String, val sizeInfo: SizeInfo)

        GlobalScope.launch {
            flow {
                //没隔一秒发送一个相同的信息
                emit(Computer("apple", SizeInfo(100, 300)))
                delay(1000)
                emit(Computer("apple", SizeInfo(100, 300)))
                delay(1000)
                emit(Computer("apple", SizeInfo(100, 300)))
            }.distinctUntilChanged().collectLatest {
                Log.d("info", "computer info")
            }
        }

    }
```

执行上述方法，打印的日志如下
![log日志](log.png)

可以发现其并没有过滤掉相同的信息，下面将 `SizeInfo` 类的定义由 `class` 改为 `data class`，再次运行此方法，打印的日志如下
![log2日志](log2.png)

可以发现成功过滤掉了相同的信息

#### 问题分析

要分析为什么将 `data class` 改为 `class` 后过滤就会失败，首先来看看 `distinctUntilChanged()`具体做了什么操作

```kotlin
//keySelector 默认用来生成key的函数
//areEquivalent 默认用来比较是否相等的函数
public fun <T> Flow<T>.distinctUntilChanged(): Flow<T> =
    when (this) {
        is StateFlow<*> -> this // state flows are always distinct
        else -> distinctUntilChangedBy(keySelector = defaultKeySelector, areEquivalent = defaultAreEquivalent)//
    }

//用来比价是否相等的函数，通过==来比较两个对象是否结构相等，用的equals方法
private val defaultAreEquivalent: (Any?, Any?) -> Boolean = { old, new -> old == new }

```

通过上述代码我们可以看到 `distinctUntilChanged()`默认使用结构相等(`equals`)来判断两个对象是否相同，我们再来看一下我们使用 `data class` 定义 `SizeInfo` 反编译后的 java 代码中 `equals` 方法的具体实现，通过对比各个属性的值是否相等来判断两个对象是否结构性相等，

```java
//data class SizeInfo
//重写equals方法
 public boolean equals(@Nullable Object var1) {
            if (this != var1) {
               if (var1 instanceof SizeInfo) {
                  SizeInfo var2 = (SizeInfo)var1;
                  //比较对象的属性是否相等
                  if (this.width == var2.width && this.height == var2.height) {
                     return true;
                  }
               }

               return false;
            } else {
               return true;
            }
         }
```

而使用 `class` 定义的 `SizeInfo` 由于我们没有实现 `equals` 方法，所以使用的是 `Object` 默认的 `equals` 方法,如下所示，它比较的两个对象是否相等，由于我们上面的测试代码每次都创建不同的 `SizeInfo` 对象，所以 `equals` 方法返回 false，所以哪怕两个对象的属性都相同，由于是不同的对象，`distinctUntilChanged()`也不会过滤

```java
// class SizeInfo
public boolean equals(Object obj) {
        return (this == obj);
    }
```

#### 解决方法

1. 将 `class` 定义成 `data class`
2. 自己重写实现 `class` 的 `equals` 方法，实现自己的相等性判断逻辑

#### 总结

1. 需要多了解 `kotlin` 语言的特性，遇到问题多记录
