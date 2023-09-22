<!--
title:Android apk打包过程
subtitle:Android apk打包过程
createDate:2022-11-16
updateDate:2022-11-16
tags:Android,APK打包
imagePath:img/Android apk打包过程
-->


## Android Apk 打包过程

### **`老版`**

![官方apk打包流程图](apk_build.png)

1. `res`目录下的可被编译的资源文件（如`layout`、`values`等）和`AndroidManifest.xml`经过`aapt`工具生成对应的`R.java`文件和`resouce.arsc`文件。

   - `R.java`文件是在应用层可以直接通过`resource id`进行访问；而`resouce.arsc`是`apk`在运行时，`dalivk`虚拟机用来识别的资源表；

   - `R.java`文件只是`resource id`列表，而`resource.arsc`文件会将这些`resource id`进行组装，在`apk`运行的时候，根据设备的情况采用不同的资源；

   - 另一部分不可编译的需要直接通过文件名进行访问的资源文件（`assets`、`res/raw`）就会直接通过和`.dex`文件打包到`apk`中

2. `aidl`文件通过`aidl`工具生成对应的`java`文件。对应的任务队列中的`“Task :app:compileDebugAidl NO-SOURCE”`；
3. 将`R.java`文件、`aidl`文件以及项目的源代码经过`Java`编译器编译成`.class`文件；
4. 通过`dx`工具（主要将`Java`字节码转换成`Dalivk`字节码、压缩常量池、消除冗余信息）生成运行`apk`的环境`dalivk`虚拟机可以执行的`.dex`文件，这里包括第三方的`libraries`以及`.class`文件。
5. 不可编译的资源文件、`.dex`文件通过`apkbuilder`工具直接打包到`apk`中
6. 通过`jarsinger`工具对`apk`进行签名，通常可根据`debug`还是`release`设置两种签名的`keystore`
7. 使用`zipalign`工具对`apk`中的所有资源文件对齐处理

### **`新版`**

![apk打包流程](1.png)
![流程](2.png)
