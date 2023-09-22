<!--
title:gradle调试设置
subtitle:gradle调试设置
createDate:2022-11-16
updateDate:2022-11-16
tags:Android,Gradle
imagePath:img/gradle调试设置
-->


## `Gradle` 调试设置

开发过程中如果碰到 `gradle` 相关问题，如果不知道如何调试，可能会觉得无法下手，特别是涉及到项目构建问题。下面分享 `gradle` 调试方式

1. 新建一个 `Module`,命名为 `buildSrc`

   - 名字必须是 `buildSrc`，因为运行 `Gradle` 时会检查项目中是否存在一个名为 `buildSrc` 的目录
   - 如果 `buildSrc` 是一个 `Groovy` 项目，`Gradle` 默认会自动配置该 `Gradle` 插件，在项目中直接 `apply` 开发的插件即可,关于在 `buildSrc` 创建自定义插件见[自定义插件](https://blog.csdn.net/Notzuonotdied/article/details/105740915)

2. 修改 `buildSrc` 内的 `build.gradle` 文件

```gralde

plugins {
    id 'groovy' //标记其为gradle插件
}
java {
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
}

repositories {
    mavenCentral()
    google()
}

dependencies {
    implementation gradleApi()
    implementation localGroovy()
    implementation 'com.android.tools.build:gradle:4.1.1' //注意，此处引入的gradle版本号要与项目中引用的版本号相同
}

```

3. 删除 `buildSrc` 内的 src 目录下的所有文件
4. 添加并选中运行配置

   打开 `Edit configurations` 添加 `Remote JVM Debug` 按默认配置即可，点击 `apply`
   ![添加运行配置](screen1.jpg)
   ![配置参数](screen2.jpg)

5. 输入打包命令 启动 gradle 进程

   ./gradlew assembleDebug -Dorg.gradle.debug=true --no-daemon

   - -Dorg.gradle.debug=true 表示 gradle 进程启动后等待调试器 attach 上才开始运行
   - --no-daemon 表示是否开启 daemon 进程，不加这个参数默认是会开启 daemon 的。

6. 找到需要调试的类，添加断点，点击 `debug` 即可调试

   例如我想调试一下 `gradle` 任务看一下创建配置流程，可以找到 `TaskManager` 类，在 `createTasks` 方法中添加断点
   ![断点调试](screen3.jpg)
