<!--
title:gradle由4.2.1升级到7.2.2过程中遇到的问题
subtitle:近期由于项目开发需要，打算接入React Native进行混合开发，由于 React Native 新版本(0.71.2)默认使用的 android gradle 插件版本为 7.2.2，而原生项目依然使用的是 4.2.1 版本，本着用新不用旧的原则，打算对原生项目进行gradle插件的升级，特此记录升级过程中遇到的问题
createDate:2023-02-20
updateDate:2023-03-08
tags:Android,React Native,Gradle,问题记录
imagePath:img/gradle由4.2.1升级到7.2.2过程中遇到的问题
-->

## Gradle 由 4.2.1 升级到 7.2.2 的过程中遇到的问题

#### 近期由于项目开发需要，打算接入`React Native`进行混合开发，由于 `React Native` 新版本(`0.71.2`)默认使用的 `android gradle` 插件版本为 `7.2.2`，而原生项目依然使用的是 `4.2.1` 版本，本着用新不用旧的原则，打算对原生项目进行 `gradle` 插件的升级，升级过程中遇到如下问题，特此记录

1. <font color=red> Using insecure protocols with repositories, without explicit opt-in, is unsupported. Switch Maven repository 'maven(http://developer.huawei.com/repo/)' to redirect to a secure protocol (like HTTPS) or allow insecure protocols.</font>

   相关错误:
   ![错误1](%E9%97%AE%E9%A2%981.png)

   错误原因:  
   `gradle` 升级后默认不允许 `maven` 仓库远程路径使用 `http` 非安全连接，

   解决方案:

   1. 添加`allowInsecureProtocol true`告诉 gradle 我要使用不安全的连接，如下图  
      ![解决11](%E8%A7%A3%E5%86%B311.png)
   2. 使用 `https` 连接

   由于项目中有些 `maven` 仓库路径并且提供 `https` 的链接路径，此处我选择使用了第一种方式

2. <font color=red>groovy.lang.MissingPropertyException: Could not set unknown property 'includeCompileClasspath' for AnnotationProcessorOptions_Decorated{classNames=[], arguments={}, compilerArgumentProviders=[]} of type com.android.build.gradle.internal.dsl.AnnotationProcessorOptions</font>

   相关错误:
   ![错误2](error2.png)

   错误原因:
   原因待查,推测是此属性在高版本上已经被移除

   解决方案:  
   注视掉此属性
   ![解决21](error21.png)

3. <font color=red>Failed to apply plugin 'com.android.internal.application'. Android Gradle plugin requires Java 11 to run. You are currently using Java 1.8.</font>

   相关错误:
   ![错误3](error3.png)

   错误原因:
   高版本 `gradle` 需要使用的 `java` 版本为 `java11`,目前使用的是 `java1.8`

   解决办法:  
   更改 `gradle` 项目构建默认使用的 `java` 的版本为 `java11`,操作步骤如下图,在第三部选择 `java11` 就可以
   ![解决3](error31.png)
