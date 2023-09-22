<!--
title:buildSrc引入gradle插件时报错
subtitle:buildSrc引入 android gradle插件时报错 Plugin request for plugin already on the classpath must not include a version
createDate:2022-11-16
updateDate:2022-11-16
tags:Android,Gradle,问题记录
imagePath:img/gradle buildSrc引入gradle插件时报错
-->


### `buildSrc` 引入 `android gradle` 插件时报错 `Plugin request for plugin already on the classpath must not include a version`

#### 问题描述

最近在学习自定义 `gradle` 插件时，需要使用到 `android gradle` 插件中的 `Transform` 类 通过 `implementation` 方式引入后一直提示如下错误
`Plugin request for plugin already on the classpath must not include a version `

buildSrc 文件夹下的 build.gradle 的引用如下
![引入方式](2.jpg)

构建后报错信息如下

```gradle
Exception is:
org.gradle.api.GradleException: Error resolving plugin [id: 'com.android.application', version: '7.2.2', apply: false]
	at org.gradle.plugin.use.internal.DefaultPluginRequestApplicator
	at org.gradle.util.internal.CollectionUtils.collect(CollectionUtils.java:207)
	at org.gradle.util.internal.CollectionUtils.collect(CollectionUtils.java:201)
	....//报错信息删减
	at org.gradle.internal.concurrent.ThreadFactoryImpl$ManagedThreadRunnable.run(ThreadFactoryImpl.java:61)
Caused by: org.gradle.plugin.management.internal.InvalidPluginRequestException: Plugin request for plugin already on the classpath must not include a version
	at org.gradle.plugin.use.resolve.internal.AlreadyOnClasspathPluginResolver.resolve(AlreadyOnClasspathPluginResolver.java:62)
	at org.gradle.plugin.use.internal.DefaultPluginRequestApplicator.resolveToFoundResult(DefaultPluginRequestApplicator.java:212)
	... 167 more
```

#### 解决办法

`google` 也没有找到合适的解决方案，后来删除项目根目录的 `build.gradle` 中的插件的定义才得意解决
![解决方法](1.png)

#### 原因推测`(个人推测，未验证)`

在运行 `gradle` 时，会检查项目中是否存在一个名为 `buildSrc` 的目录，然后 `gradle` 会自动编译并测试这段代码，并将其放入到构建脚本的类路径(`classpath`)中，由于我们的 `buildSrc` 中引用了 `android gradle` 插件，这时插件会被放入到类路径中，这时再在项目根目录的 `build.gradle` 中定义插件，就会导致类路径重复，所以报错

#### 遗留问题

通过 `buildSrc` 创建插件的方式会报错，但是通过单独的 `library` 的方式创建插件，即使引入了 `android gradle` 插件也不会报错
