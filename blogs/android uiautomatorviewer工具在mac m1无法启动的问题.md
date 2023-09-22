<!--
title:android uiautomatorviewer工具在mac m1无法启动的问题
subtitle:android uiautomatorviewer工具在mac m1无法启动的问题
createDate:2022-11-16
updateDate:2022-11-16
tags:Mac M1,uiautomatorviewer,问题记录
imagePath:img/android uiautomatorviewer工具在mac m1无法启动的问题
-->


### 关于布局查看工具 uiautomatorviewer 在 mac m1 芯片上无法启动问题的解决

最近想查看一下其他 `app` 的页面布局，发现 `uiautomatorviewer` 无法打开，提示如下问题

`SWT folder '/Users/tongfu/Library/Android/sdk/tools/lib/aarch64' does not exist. Please export ANDROID_SWT to point to the folder containing swt.jar for your platform.`

提示 `lib` 目录下找不到 `aarch64` 目录，查看 `lib` 目录，发现存在 `x86`,`x86_64` 目录，不存在 `aarch64` 目录
![文件目录](1.jpg)

尝试创建 `aarch64` 目录，下载 `aarch64` 位 `swt.zip` 文件，解压后拷贝的 `swt.jar` 文件到此目录，启动仍然报错
![报错信息](2.png)

后来查找发现不能下载 `aarch64` 位的包，需要下载 `x86_64` 的包，然后重命名 `swt.jar` 为 `swt2.jar`,然后拷贝到 `aarch64`
同时把原 `x86_64` 文件夹下的 `swt.jar` 文件也拷贝到 `aarch64` 中一份

具体步骤

1. 在 `lib` 目录下创建 `aarch64` 文件夹
2. 拷贝 `x86_64` 文件夹下的 `swt.jar` 文件到 `aarch64` 文件夹
3. 下载此链接下的[swt 源文件](https://www.eclipse.org/downloads/download.php?file=/eclipse/downloads/drops4/R-4.19-202103031800/swt-4.19-cocoa-macosx-x86_64.zip),解压
4. 找到解压后的文件夹中的 `swt.jar` 文件，重命名为 `swt2.jar`
5. 拷贝 `swt2.jar` 到 `aarch64` 文件夹

重新执行 `uiautomatorviewer` 命令，发现可以正常启动抓取 `ui` 布局信息
