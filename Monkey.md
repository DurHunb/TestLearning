# 一、adb详解



## 1、何为adb

> apk：安卓应用安装包
>
> sdk：
>
> - SDK 就是 Software Development Kit 的缩写，翻译过来——软件开发工具包。
>
> - 这是一个覆盖面相当广泛的名词，辅助开发某一类软件的相关文档、范例和工具的集合都可以叫做SDK。

- adb（Android Debug Bridge）是 Android sdk 的一个工具

- adb是用来链接<font color='red'>安卓手机</font>和PC端的桥梁，要有adb作为二者之间的维系，才能让用户在电脑上对手机进行全面的操作

- Android的初衷是用adb这样的一个工具来协助开发人员在开发Android应用的过程中，更快更好的调试apk。

  因此，adb具有安装卸载apk、拷贝推送文件、查看设备硬件信息、查看应用程序占用资源、在设备执行Shell命令等功能

  

## 2、adb的组成

- 客户端`client`：运行在你的电脑上，可以通过adb命令调用起一个客户端
- 服务器`server`：运行在电脑的后台，负责管理`client`和`daemon`进行通信
- 守护进程`daemon`：运行在模拟器或者Android设备的后台