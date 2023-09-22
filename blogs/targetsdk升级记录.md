<!--
title:targetsdk升级记录
subtitle:targetsdk升级记录
createDate:2023-09-12
updateDate:2023-09-12
tags:Android,AndroidSDK
imagePath:img/targetsdk升级记录
-->


## Android targetSdk 升级到 30 时遇到的问题记录

#### 最近小米应用市场发布通知，需要将应用的目标版本号提升到最低为 `30`,即 `targetSdkVersion>=30`,为配合应用市场规则，对项目进行了升级，升级过程遇到了一些问题，特此记录

#### 1: `Caused by: java.lang.IllegalStateException: Expected Android API level 21+ but was 30`

问题详情：

```sh
Caused by: java.lang.IllegalStateException: Expected Android API level 21+ but was 30
    at okhttp3.internal.platform.AndroidPlatform$Companion.buildIfSupported(AndroidPlatform.kt:370)
    at okhttp3.internal.platform.Platform$Companion.findPlatform(Platform.kt:204)
    at okhttp3.internal.platform.Platform$Companion.access$findPlatform(Platform.kt:178)
    2020-09-16 12:37:07.645 6480-6480/lv.ltt.gasogmp.dev_v3 E/AndroidRuntime:     at
    okhttp3.internal.platform.Platform.<clinit>(Platform.kt:179)
```

问题原因：

`okhttp` 版本过低,项目目前使用的是 `com.squareup.okhttp3:okhttp:3.14.0`,不支持 `AndroidSdk 30`

解决办法：

1. 使用 `okhttp4.x` 此种方式代码改动过大，暂时不采用
2. 使用 `okhttp3.14.7` 或更高版本

参考文章:  
[Expected Android API level 21+ but was 30](https://stackoverflow.com/questions/63917431/expected-android-api-level-21-but-was-30)

#### 2： `ijkplayer` 播放视频崩溃

问题详情:

Fatal signal 7 (SIGBUS), code 1 (BUS_ADRALN)

问题原因:

从 `Android 11` 开始，对于 64 位进程，所有堆分配都具有一个由实现定义的标记，该标记在具有对 ARM Top-byte Ignore (TBI) 的内核支持的设备上的指针顶部字节中设置。在回收期间检查该标记时，任何修改此标记的应用都会被终止

解决办法:

在 `AndroidManifest.xml` 文件中添加以下内容：

```xml
  <application android:allowNativeHeapPointerTagging="false">
  ...
  </application>
```

如果提示无法找到 `allowNativeHeapPointerTagging` 属性,需要将 `compileSdkVersion` 版本升级到 30 以上

参考文章:  
[已加标记的指针](https://source.android.google.cn/docs/security/test/tagged-pointers?hl=zh-cn)  
[Fatal signal 7 (SIGBUS), code 1 (BUS_ADRALN)](https://github.com/bilibili/ijkplayer/issues/5334#issuecomment-926573595)
