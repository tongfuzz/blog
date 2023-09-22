<!--
title:移动固态硬盘+Mac M1编译安卓源码,写入真机记录
subtitle:移动固态硬盘+Mac M1编译安卓源码,写入真机记录
createDate:2023-08-14
updateDate:2023-08-15
tags:Android,源码编译
imagePath:img/移动固态硬盘+Mac M1编译安卓源码,写入真机记录
-->


## 移动固态硬盘+Mac M1 编译安卓源码,写入真机记录

#### 为何要用移动固态硬盘？

1. 购买的 mac m1 笔记本硬盘只有 500G 左右大小，编译 android 源码时需要大概 250G 左右的存储空间，一开始是下载编译到本机上，后来由于 mac 经常提示磁盘空间不足，而源码只在我需要调试源码的时候有用到，平常并不会使用到，所以决定在移动硬盘上重新编译
2. 移动固态硬盘太便宜了，500 左右就能买到读写速度达 2G 左右，容量为 1T 的硬盘(小米移动固态硬盘)

#### 为何不再 ubuntu 上编译源码
编译 aosp 最佳系统为 ubuntu，并且自 2021 年 6 月 22 日起，aosp 将不再支持在 Windows 或 MacOS 上进行构建，但是 ubuntu 支持 mac m1 的版本最低为 20.04，而 google 官方最高支持 aosp 开发编译的版本号为 18.04 ，所以选择在 macos 上直接编译，虽然官方说不再支持 macos，但是仍然可以用来编译，只需要解决编译过程中出现的问题即可

#### 准备磁盘

使用磁盘工具建立新的分区，格式为 APFS（区分大小写）,大小设置为 250G

#### 安装配置

1. 安装 python、git 和 Xcode
2. 配置 git config
   ```shell
      git config --global user.name "Your Name"
      git config --global user.email "you@example.com"
   ```
3. 下载 repo  
   参考[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)
   ```shell
   mkdir ~/bin
   PATH=~/bin:$PATH
   curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
   chmod a+x ~/bin/repo
   ```
4. 下载源码  
    参考[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)

   - 建立工作目录:  
     mkdir WORKING_DIRECTORY  
     cd WORKING_DIRECTORY
   - 初始化仓库:  
     repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest  
     如果需要某个特定的 Android 版本([列表](https://source.android.com/docs/setup/about/build-numbers?hl=zh-cn#source-code-tags-and-builds)),注意查看列表，选择与自己真机相匹配的系统 android 版本,例如需要将系统写入到 pixel3 手机上，那我们可以选择支持 pixel 的版本，如 android-11.0.0_r46,记录下 build id 以供下载驱动时使用，如 android-11.0.0_r46 匹配的 build id 为 RQ3A.211001.001：  
     repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest -b android-11.0.0_r46

   - 同步源码树  
     此处需要较长时间，可以选择在晚间同步
     repo sync

5. 下载所需的驱动文件  
   在[驱动下载页面](https://developers.google.com/android/drivers?hl=zh-cn#bluelinerp1a.200720.009)下载对应 build id 的驱动程序二进制文件，下载完成后解压到 WORKING_DIRECTORY，解压缩出来两个 sh 脚本文件，执行两个脚本文件即可

6. 开始编译

   - 在 aosp 目录下执行 envsetup.sh 脚本初始化环境

   ```shell
   source build/envsetup.sh
   ```

   - 清除之前编译的输出文件(非必需，如果在编译过程中，只是一些依赖出现问题，而不是修改了代码，那么可以不执行此步骤)

   ```shell
   make clobber
   ```

   - 选择编译目标

   ```shell
   lunch
   ```

   - 执行上面的命令后终端回输出如下提示信息:

   ```shell
   You're building on Darwin
   Lunch menu... pick a combo:
      1. aosp_arm-eng
      2. aosp_arm64-eng
      3. aosp_blueline-userdebug
      4. aosp_blueline_car-userdebug
      5. aosp_bonito-userdebug
      6. aosp_bonito_car-userdebug
      7. aosp_bramble-userdebug
      8. aosp_car_arm-userdebug
      9. aosp_car_arm64-userdebug
      10. aosp_car_x86-userdebug
      11. aosp_car_x86_64-userdebug
      12. aosp_cf_arm64_auto-userdebug
      13. aosp_cf_arm64_phone-userdebug
      14. aosp_cf_x86_64_phone-userdebug
      15. aosp_cf_x86_auto-userdebug
      16. aosp_cf_x86_phone-userdebug
      17. aosp_cf_x86_tv-userdebug
      18. aosp_coral-userdebug
      19. aosp_coral_car-userdebug
      20. aosp_crosshatch-userdebug
      21. aosp_crosshatch_car-userdebug
      22. aosp_flame-userdebug
      23. aosp_flame_car-userdebug
      24. aosp_redfin-userdebug
      25. aosp_sargo-userdebug
      26. aosp_sunfish-userdebug
      27. aosp_trout_arm64-userdebug
      28. aosp_trout_x86-userdebug
      29. aosp_x86-eng
      30. aosp_x86_64-eng
      31. arm_krait-eng
      32. arm_v7_v8-eng
      33. armv8-eng
      34. armv8_kryo385-eng
      35. beagle_x15-userdebug
      36. beagle_x15_auto-userdebug
      37. car_x86_64-userdebug
      38. db845c-userdebug
      39. fuchsia_arm64-eng
      40. fuchsia_x86_64-eng
      41. hikey-userdebug
      42. hikey64_only-userdebug
      43. hikey960-userdebug
      44. hikey960_tv-userdebug
      45. hikey_tv-userdebug
      46. pixel3_mainline-userdebug
      47. poplar-eng
      48. poplar-user
      49. poplar-userdebug
      50. qemu_trusty_arm64-userdebug
      51. silvermont-eng
      52. uml-userdebug
      53. yukawa-userdebug
      54. yukawa_sei510-userdebug

   Which would you like? [aosp_arm-eng]
   ```

   此时你需要选择一个组合，由于笔者使用的是 pixel3 手机，平台代号为 blueline,构建类型我们选择 userdebug，此构建类型具有 root 权限与调试功能，是进行调试的首选编译类型,当然你也可以选择 user 构建类型，user 构建类型权限受限，适用于生产环境，blueline+userdebug，所以笔者选择组合 3 进行构建

   - 开始编译代码  
     使用 make 编译代码，GNU Make 同样可以借助 -jN 参数处理并行任务，通常使用的任务数 N 介于编译时所用计算机上硬件线程数的 1-2 倍之间，这里笔者选择 10

   ```shell
      make -j10
   ```

   接下来就是漫长的等待......(笔者使用 mac m1,构建时间 2 个小时左右)，构建过程中会出现各种问题，参见最后问题列表

7. 刷入真机

   - 解锁 oem  
     如果你的手机之前没有刷过机，那么手机首先要 oem 解锁，才能使用 fastboot 命令，同样，使用 Pixel 为例
     ```
     adb reboot bootloader
     fastboot devices
     fastboot oem unlock
     fastboot oem device-infos
     ```
     解锁过程中出现问题，自行 google 查询即可
   - 在编译完成的 out 目录下，使用 fastboot flashall 刷机即可

8. 问题列表

   - Could not find a supported mac sdk
     错误信息

     ```shell
        --- FAIL: TestVndkUsingCoreVariant (0.04s)
     cc_test.go:524: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:524: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:524: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     --- FAIL: TestVndkWhenVndkVersionIsNotSet (0.03s)
     cc_test.go:530: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:530: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:530: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:530: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:530: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:530: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     --- FAIL: TestVndkWithHostSupported (0.04s)
     cc_test.go:434: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:434: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:434: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:434: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:434: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     cc_test.go:434: "Could not find a supported mac sdk: [\"10.10\" \"10.11\" \"10.12\" \"10.13\" \"10.14\" \"10.15\"]"
     FAIL
     21:30:32 soong bootstrap failed with: exit status 1
     ninja: build stopped: subcommand failed.
     #### failed to build some targets (29 seconds) ####
     ```

     问题原因:找不到支持列表中指定的 mac sdk

     解决办法:

     1. 直接修改/x86_darwin_host.go
        - 首先按照下面的方法查看自己电脑上的 mac sdk：
        ```shell
           cd /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs
           ls
        ```
        - 如果提示找不到此目录，请在 AppStore 下载并安装 Xcode
        - 如果已经安装过 Xcode，会打印出当前 XCode 提供的 SDK 版本号，其中 MacOSX.sdk 为指向 MacOSX12.X.sdk 的软链接，如下:
        ```shell
           //Apple Silicon平台
           MacOSX.sdk  MacOSX12.3.sdk
        ```
        - 在/build/soong/cc/config/x86_darwin_host.go 文件中找到 “darwinSupportedSdkVersions“ 添加 MacOSX.sdk 对应的版本号
        ```shell
           darwinSupportedSdkVersions = []string{
                 "10.10",
                 "10.11",
                 "10.12",
                 "10.13",
                 "10.14",
                 "10.15",
                 "12.1",
           }
        ```
     2. 下载支持的 sdk(笔者推荐的解决方法)
        - 从[https://github.com/phracker/MacOSX-SDKs/releases](https://github.com/phracker/MacOSX-SDKs/releases)下载当支持的 sdk 版本
        - 解压并将 sdk 放入/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs 目录下
          （比较坑的是，网上有很多解决此问题的指导到这里就结束了，所以再次开始编译仍然会报相同的错！）
        - 修改系统支持的最小 SDK 版本
          进入/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/目录
          使用 Xcode 打开 Info.plist 文件，将 MinimumSDKVersion 改成你想支持的最低版本，笔者这里修改成了 10.15
        - 由于该文件和所在目录都有权限限制，因此无法直接修改,先修改文件权限
        ```shell
        sudo chmod 777 MacOSX.platform
        sudo chmod 777 Info.plist
        ```
        - 再次执行 make 命令编译

   - 问题 2 too many open files
     错误日志如下:

     ```shell
        FAILED: out/soong/build.ninja
        out/soong/.bootstrap/bin/soong_build -t -l out/.module_paths/Android.bp.list -b out/soong -n out -d out/soong/build.ninja.d -globFile out/soong/.bootstrap/build-globs.ninja -o out/soong/build.ninja Android.bp
        error: prebuilts/sdk/Android.bp:25:1: module "metalava-sdk-android-jars": glob: fcntl: too many open files
        error: system/timezone/distro/tools/Android.bp:16:1: module "create_time_zone_distro" variant "darwin_common": glob: open /Volumes/AOSP/WORKING_DIRECTORY/system/timezone/distro/tools/src/main/com/android/timezone: too many open files
        error: cts/tools/vm-tests-tf/Android.bp:58:1: module "vmtests-dfh-dex-generated" variant "darwin_common": glob: fcntl: too many open files
     ```

     问题原因: 打开的文件数量超过了系统限制

     解决方法:
     把系统限制改大一点,执行以下命令

     ```shell
        ulimit -S -n 2048
     ```

   - Changing the VNDK library list is not allowed in API locked branches **\*** diff: unrecognized option \*\*\*\*  
     错误日志如下:

     ```shell
        [  7% 7816/108971] build out/target/product/generic_arm64/obj/PACKAGING/vndk_intermediates/check-list-timestamp
         FAILED: out/target/product/generic_arm64/obj/PACKAGING/vndk_intermediates/check-list-timestamp
         /bin/bash -c "(( diff --old-line-format=\"Removed %L\"    --new-line-format=\"Added %L\"      --unchanged-line-format=\"\"    build/make/target/product/gsi/29.txt out/target/product/generic_arm64/obj/PACKAGING/vndk_intermediates/libs.txt     || ( echo -e \" error: VNDK library list has been changed.\\n\" \"       Changing the VNDK library list is not allowed in API locked branches.\"; exit 1 )) ) && (mkdir -p out/target/product/generic_arm64/obj/PACKAGING/vndk_intermediates/ ) && (touch out/target/product/generic_arm64/obj/PACKAGING/vndk_intermediates/check-list-timestamp )"
         diff: unrecognized option `--old-line-format=Removed %L'
         usage: diff [-aBbdilpTtw] [-c | -e | -f | -n | -q | -u] [--ignore-case]
                     [--no-ignore-case] [--normal] [--strip-trailing-cr] [--tabsize]
                     [-I pattern] [-F pattern] [-L label] file1 file2
               diff [-aBbdilpTtw] [-I pattern] [-L label] [--ignore-case]
                     [--no-ignore-case] [--normal] [--strip-trailing-cr] [--tabsize]
                     [-F pattern] -C number file1 file2
               diff [-aBbdiltw] [-I pattern] [--ignore-case] [--no-ignore-case]
                     [--normal] [--strip-trailing-cr] [--tabsize] -D string file1 file2
               diff [-aBbdilpTtw] [-I pattern] [-L label] [--ignore-case]
                     [--no-ignore-case] [--normal] [--tabsize] [--strip-trailing-cr]
                     [-F pattern] -U number file1 file2
               diff [-aBbdilNPprsTtw] [-c | -e | -f | -n | -q | -u] [--ignore-case]
                     [--no-ignore-case] [--normal] [--tabsize] [-I pattern] [-L label]
                     [-F pattern] [-S name] [-X file] [-x pattern] dir1 dir2
               diff [-aBbditwW] [--expand-tabs] [--ignore-all-blanks]
                     [--ignore-blank-lines] [--ignore-case] [--minimal]
                     [--no-ignore-file-name-case] [--strip-trailing-cr]
                     [--suppress-common-lines] [--tabsize] [--text] [--width]
                     -y | --side-by-side file1 file2
               diff [--help] [--version]
         error: VNDK library list has been changed.
               Changing the VNDK library list is not allowed in API locked branches.
         12:06:16 ninja failed with: exit status 1

         #### failed to build some targets (04:31 (mm:ss)) ####
     ```

     错误原因: Apple BSD 版本的 diff 不支持--old-line-format=Removed %L 选项

     解决办法: 下载 diffutils，将 diff 命令指向下载 diffutils

     ```shell
      brew install diffutils
     ```

     添加 diff 命令的指向(也可将此命令添加到你的 bash 文件中)

     ```shell
      alias diff="/opt/homebrew/bin/diff"
     ```

9. 参考文章:  
   [Intel/Apple Silicon 架构 MacOS 平台编译 AOSP 源码](https://www.jianshu.com/p/df0dcc935cca)  
   [从零开始搭建 Android 源码调试环境](https://0xfree.top/2019/04/22/how-to-build-android-source-code/#%E8%B0%83%E8%AF%95%E6%89%8B%E6%9C%BA%E9%80%89%E6%8B%A9)  
   [error: VNDK library list has been changed. Changing the VNDK library list is not allowed in API locked branches](https://stackoverflow.com/questions/74568500/failed-out-target-product-generic-arm64-obj-packaging-vndk-intermediates-check)  
   [Android 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)  
   [构建优雅的 Android Kernel 内核和 AOSP 平台代码阅读环境](https://juejin.cn/post/7249987493939445818)  
   [Android 官方源码编译教程](https://source.android.google.cn/docs/setup/start?hl=zh-cn)
