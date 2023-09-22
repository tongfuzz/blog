<!--
title:ijkplayer编译
subtitle:ijkplayer编译
createDate:2023-09-12
updateDate:2023-09-12
tags:Android,IJKplayer
imagePath:img/ijkplayer编译
-->


## ijkplayer 编译记录

#### 引言

最近需求不是很多，准备闲暇时将项目的 `targetSdk` 版本号升级一下，升级过程中碰到的问题大部分都顺利解决，可是有一个 `ijkplayer` 播放器崩溃的问题始终无法排查到原因，由于现在使用的 `ijkplayer` 播放器是很久以前编译生成的(虽然以前编译过，但是几乎已经忘了怎么编译的了，果然好记性不如烂笔头)，猜测可能是需要重新编译最新的 `ijkplayer` 播放器，于是准备重新编译新的 `ijkplayer` 试试，此篇文章主要用来记录编译过程中遇到的问题以及解决方法(不记录好像永远记不住)

#### 环境准备

- homebrew
- git
- yasm
- Android Sdk
- Android NDK (官方使用的是 `android-ndk-r10e`,使用 `r14b` 也可以，这里最好与官方保持一致)
- Mac M1(也可以选择 `linux`,最好不要使用 `windows`)

#### 环境设置

在环境变量中(`.bash_profile` 或者 `.profile`)加入 `Android SDK` 与 `Android NDK` 的路径

```sh

# 名称不能改动

export ANDROID_SDK=/Users/{你的sdk路径}/Android/sdk
export ANDROID_NDK=/Users/{你的ndk路径}/android-ndk-r10e

# 使用source .bash_profile 使配置生效
```

#### 开始编译

1. 下载源码(确保下载了 `git`)

   ```sh
   # 克隆到ijkplayer-android目录
    git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android

    # 进入ijkplayer-android目录
    cd ijkplayer-android

    # 检出最新版本
    git checkout -B latest k0.8.8

   ```

2. 配置编译选项  
   编译时我们可以选择我们的配置选项，比如支持什么协议，支持什么音视频类型，官方为我们提供了是三个模版

   - module-lite.sh  
     `默认配置`，支持较少的编解码器与格式（编译耗时较短）
   - module-default.sh  
     支持较多的编解码器与格式 (编译耗时较长)
   - module-lite-hevc.sh
     支持较少的编解码器与格式(包含 hevc 函数)

   这里我们使用支持较多的编解码器与格式`module-default.sh`，后期挑选合格的 `so` 库到项目中即可

   ```sh
     # 进入到config目录
     cd config
     # 删除默认配置
     rm module.sh
     # 创建新的配置，并链接到module-default.sh
     ln -s module-default.sh module.sh
   ```

3. 初始化

   ```sh
   # 初始化android
   ./init-android.sh
   # 初始化Android-openssl以支持https协议
   ./init-android-openssl.sh
   ```

   初始化操作会下载其所需要的源码，下载过程会比较慢，耐心等待即可

4. 编译

   1. 对于 `Mac m1 m2 `芯片，需要关闭恶意软件检测(非 `Mac m1 m2` 可不执行这一步)
      ```sh
      sudo spctl --master-disable
      ```
   2. 开始编译

      1. 进入到 `android/contrib` 文件夹
         ```sh
         cd android/contrib
         ```
      2. 编译 `openssl` 以支持 `https`

         ```sh
         ./compile-openssl.sh clean
         ./compile-openssl.sh all
         ```

         当命令行输出如下信息时说明编译成功

         ```sh
         --------------------
         [*] link openssl
         --------------------

         --------------------
         [*] Finished
         --------------------
         ```

         这一步的编译可能遇到以下失败情况

         1. 失败情况 1:  
            如果编译失败，如下所示提示很多引用问题，
            ```sh
            ../libcrypto.a(x509type.o):x509type.c:function X509_certificate_type: error: undefined reference to 'OBJ_find_sigid_algs'
            ../libcrypto.a(ec_ameth.o):ec_ameth.c:function ec_pkey_ctrl: error: undefined reference to 'OBJ_find_sigid_by_algs'
            ../libcrypto.a(ec_ameth.o):ec_ameth.c:function ec_pkey_ctrl: error: undefined reference to 'OBJ_find_sigid_by_algs'
            ../libcrypto.a(ec_pmeth.o):ec_pmeth.c:function pkey_ec_ctrl_str: error: undefined reference to 'OBJ_ln2nid'
            ../libcrypto.a(asn_moid.o):asn_moid.c:function oid_module_init: error: undefined reference to 'OBJ_create'
            ../libcrypto.a(asn_moid.o):asn_moid.c:function oid_module_init: error: undefined reference to 'OBJ_create'
            collect2: error: ld returned 1 exit status
            make[2]: *** [link_app.] Error 1
            make[1]: *** [openssl] Error 2
            make: *** [build_apps] Error 1
            ```
            我们需要修改 `do-detect-env.sh` 文件，将多线程编译改成单线程编译,具体步骤如下
            - 进入 `android/cnotrib/tools` 文件夹
            - 打开 `do-detect-env.sh` 文件,找到相应代码，按如下方式修改  
              ![修改记录](ijkplayer编译1.png)
            - 返回上一层目录后重新运行上面的脚本进行编译

      3. 编译 `ffmpeg`

         ```sh
         ./compile-ffmpeg.sh clean
         ./compile-ffmpeg.sh all
         ```

         当命令行输出如下信息时说明编译成功

         ```sh
         --------------------
         [*] create files for shared ffmpeg
         --------------------

         --------------------
         [*] Finished
         --------------------
         # to continue to build ijkplayer, run script below,
         sh compile-ijk.sh
         ```

         这一步的编译可能会遇到以下问题失败情况

         1. 失败情况 1:
            `Unknown option "--disable-ffserver"`错误，这是由于 ffmpeg4.0 移除了 ffserver,我们需要修改 `config` 文件夹下的 `moudle-default.sh` 配置文件(本来应该修改 `module.sh`，但是由于我们将 `module.sh` 软链到 `module-default.sh`，所以修改 `module-default.sh` 文件即可),注释 `vda` 和 `ffserver` 两个配置即可
            ![注释配置](ijkplayer编译2.png)
         2. 失败情况 2:
            `linux/perf_event.h: No such file or directory` 错误,我们同样需要修改 `module-default.sh`，修改内容如下，我们在配置文件结尾为其添加了两项配置
            ![添加配置](ijkplayer编译3.png)

      4. 编译 `ijkplayer`

         如果是非 `Mac m1 m2` 芯片的编译环境，直接执行如下命令编译即可

         ```sh
          #返回到上一级文件夹，因为compile-ijk.sh文件在android文件夹中而非android/contrib文件夹
          cd ..
          #开始编译
          compile-ijk.sh all
         ```

         对于 `Mac m1 m2` 芯片我们需要修改 `ndk-build` 文件，添加 `arm64` 架构的支持，具体方式如下

         - 定位到 ndk 目录,以文本文件方式打开 `ndk-build` 文件
         - 找到如下代码,在`x86_64|amd64`后添加`|arm64`

         ```sh
         HOST_ARCH=$(uname -m)
         case $HOST_ARCH in
            i?86) HOST_ARCH=x86;;
            x86_64|amd64|arm64) HOST_ARCH=x86_64;;
            *) echo "ERROR: Unknown host CPU architecture: $HOST_ARCH"
               exit 1
         esac
         log "HOST_ARCH=$HOST_ARCH"
         ```

         - 回到原来的 android/文件夹，重新执行如下命令

         ```sh
         compile-ijk.sh all
         ```
      5. 获取自己需要的so库
         编译完成后，我们可以在`ikjplayer`文件夹中获取我们需要的`so`库，例如，如果需要`arm64`的库，那我们去`ijkplayer-android/android/ijkplayer/ijkplayer-arm64/src/main/libs/arm64-v8`文件夹拷贝到自己的项目中即可

至此`ijkplayer`就编译完成了

5. 参考文章  
   [Mac m2芯片 全量编译ijkplayer生成Android库](https://blog.csdn.net/gfg156196/article/details/115496387)  
   [ijkplayer编译：在2022年使用M1芯片遭受毒打](https://www.jianshu.com/p/22405a692c18)   
   [Mac OS 10.15.6 编译openssl报错](https://github.com/bilibili/ijkplayer/issues/5113#issuecomment-1288378800)