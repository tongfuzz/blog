<!--
    title:adb命令
    subtitle:adb命令记录
    createDate:2020-9-15
    updateDate:2023-8-12
    tags:Android,ADB
    imagePath:
 -->

#### adb 命令

1. adb -d &emsp; 指定当前唯一通过 usb 连接的 Android 设备为命令目标；
2. adb -e &emsp; 指定当前唯一运行的模拟器为命令目标
3. adb -s serialNumber &emsp; 指定对应 serialNumber 号的设备/模拟器为命令目标，**最常用；**

4. adb shell dumpsys activity &emsp;查看 ActvityManagerService 所有信息

5. adb shell dumpsys activity activities &emsp; 查看 Activity 组件信息

6. adb shell dumpsys activity services &emsp; 查看 Service 组件信息

7. adb shell dumpsys activity providers &emsp; 产看 ContentProvider 组件信息

8. adb shell dumpsys activity broadcasts &emsp; 查看 BraodcastReceiver 信息

9. adb shell dumpsys activity intents &emsp; 查看 Intent 信息

10. adb shell dumpsys activity processes &emsp; 查看进程信息
11. adb devices -l &emsp; 列出所有设备
12. adb -s devicename install filepath &emsp; 安装 apk 到指定设备
13. adb shell pm uninstall packagename &emsp; 卸载应用
14. adb shell am start -a android.intent.action.VIEW &emsp; 打开应用
15. monkey 测试

    adb shell monkey -p com.medmeeting.m.zhiyi --ignore-crashes --ignore-timeouts -v -v -v 1000 2>/Users/tongfu/Documents/log/error.txt 1>/Users/tongfu/Documents/log/verbose.txt

    [monkey 测试](https://www.cnblogs.com/plokmju/p/6445975.html)使用 monkey 测试前，需要开启开发者选项中的模拟点击事件选项

    停止 monkey 测试命令:
    adb shell ps | awk '/com\.android\.commands\.monkey/ { system("adb shell kill " $2) }'

    [停止 monkey 测试](https://blog.csdn.net/u010983881/article/details/51954076)
    adb shell monkey -p com.medmeeting.m.zhiyi --throttle 100 --ignore-crashes --ignore-timeouts --pct-touch 60 --pct-motion 10 --pct-appswitch 30 -v 1000 >/Users/tongfu/Desktop/apptest_info/info.txt

16. adb logcat -v time ->log.txt &emsp;声称 log 日志
17. adb shell setprop log.tag.x DEBUG &emsp;
    这个命令设置系统属性,其中 log.tag.x 为 key， x 表示自定义的 tag，DEBUG(V, D, I, E) 表示日志级别（默认为 INFO）。
    只有当 Log.isLogger(tag, leval) 的 tag 相等， leval 大于等于 命令行设置的参数是，才进行日志输出

18. 手机无法连接到互联网 <br/>
    adb shell settings delete global captive_portal_https_url <br/>
    adb shell settings delete global captive_portal_http_url <br/>
    adb shell settings put global captive_portal_http_url http://connect.rom.miui.com/generate_204<br/>
    adb shell settings put global captive_portal_https_url https://connect.rom.miui.com/generate_204<br/>
    其他源 <br/>
    adb shell settings put global captive_portal_http_url http://captive.v2ex.co/generate_204<br/>
    adb shell settings put global captive_portal_https_url https://captive.v2ex.co/generate_204<br/>

19. 手机自动对时不正确  
    adb -d shell settings put global ntp_server asia.pool.ntp.org
