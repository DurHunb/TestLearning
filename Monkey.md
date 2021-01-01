# 一、APP稳定性测试

## 1、什么是稳定性测试

1. 稳定性测试指，软件要求能够长时间持续运行，系统是否稳定，是否能为用户持续进行服务。

2. 指标：
	- 异常的次数
	- 异常的频率
	
3. 如何进行APP稳定性测试？

	——Monkey



## 2、Monkey简介


> Monkey并不是性能测试。
>
> Monkey是随机进行各类操作指令，确保APP能够稳定，不出现任何崩溃、异常



- Monkey原理

	- 只有Android才有Monkey，其本身是安卓操作系统下的Monkey.jar文件。

		通过`adb`指令调用`Monkey.shell`文件，执行`Monkey.jar`文件，实现随机性测试

		- `Shell`脚本在Android系统存放路径为`/system/bin/monkey`

		- `Monkey.jar`包在Android系统存放路径为`/system/framework/monkey.jar`

	- Monkey是在进行调用之后，生成了系统进程，通过进程执行的。
	
	- 只有自己执行完毕或者杀掉这个进程这两种方法结束Monkey

![image-20201229231757155](D:%5Crb%5C%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99%5CLearning%5CMonkey.assets%5Cimage-20201229231757155.png)

- Monkey启动方式

	在`cmd`里输入命令行启动Monkey
	
	```
	adb shell Monkey ---→ Monkey.shell ---→ Monkey.jar ---→ 安卓进程执行测试
	```







## 3、使用Monkey



- **前置工作**

	- 准备Monkey环境

		`Android SDK`环境，[下载地址](http://www.androiddevtools.cn/)

		- `adb`是安卓`SDK`自带的，安装好`SDK`，就安装好了Monkey的环境
		- `Android SDK`环境依赖`JDK`环境

	- 设备

		真实的安卓机，或者模拟器



**步骤如下**

### 第一步：连接SDK和设备



- 先在设备中打开USB调试

```
设置————开发者选项——打开USB调试
```

- 在Windows的`cmd`中输入`adb devices`命令

	

	- **连接信息解释**

		连接的是手机：返回的结果中为`序列号 device`，表示连接成功

		连接的是模拟器：返回的结果为`IP地址：端口号 device`，表示连接成功

		`device`：已识别的设备，连接成功

		`unauthorized`：没有授权，需要在手机上授权才能连接

		`unknown`：未识别的设备

		`offline`：离线的设备



### 第二步：执行命令



- **通过`adb shell Monkey`查询命令**



#### 执行事件

- 执行100次事件

	```
	adb shell Monkey 100
	```

- 发送10个事件，持续执行时间为1分钟

	每个事件间隔6s

	```java
	adb shell Monkey --throttle 6000 10
	```

	

#### 指定包



- Monkey将只允许系统执行这些包里的Activity

	`-p <allowed-package-name1> -p <allowed-package-name2>`



- 获取包名

	```java
	adb shell pm list packages
	```

	

- 对58同城进行稳定性测试，执行100次事件

	```java
	adb shell monkey -p com.wuba 100
	```

	每隔0.5s执行一次

	```java
	adb shell monkey -p com.wuba --throttle 500 100
	```

	

#### 日志设置

- 等级

	```java
	-v 		 //Level 0	除启动提示、测试完成和最终结果之外，提供较少信息
	-v -v 	 //Level 1 	提供较为详细的测试信息，如逐个发送到Activity的事件
	-v -v -v //Level 2	提供更为详细的设置信息，如测试中被选中的或未被选中的Activity
	```

	

- 对58同城进行稳定性测试，执行100次事件，分别查看不同日志级别记录的信息情况

	```java
	adb shell monkey -p com.wuba -v 100
	adb shell monkey -p com.wuba -v -v  100
	adb shell monkey -p com.wuba -v -v -v   100
	```



- 日志导出到本地

	```java
	adb shell monkey -p com.wuba -v 100 >d:\wuba.txt 
	```






#### 复现&回归

> 在Monkey运行的过程中，肯定会发现异常、奔溃、无响应等问题，对于此类问题如何进行复现呢？



```java
-s<seed> //伪随机数生成器的seed值。如果用相同的seed值再次运行Monkey，它将生成相同的事件序列
```

执行Monkey命令生成的日志中，会有表明此次运行的seed值

- 使用场景：复现bug，回归测试

	```java
	adb shell monkey -s 1593077384911 -p com.wuba -v-v-v 100 >d:\wuba4.txt
	    //通过对比wuba.txt和wuba4.txt，就可知道两次执行的内容是否一样
	```

	

#### 防止因出现异常的运行结束

> 在Monkey的运行过程中，我们可以通过结束进程的方式来终止Monkey的运行，
>
> 当然在运行过程中遇到异常、奔溃等情况时，Monkey也会停止

为了保证执行的事件数执行完整，我们可以通过调试选项来防止在运行过程中Monkey命令的提前结束

```java
--ignore-crashes	//忽略在应用程序中出现crash问题时，导致的Monkey停止
--ignore-timeouts	//忽略在应用程序中出现ANR问题时，导致的Monkey停止（程序无响应）
--ignore-security-exceptions	//忽略在应用程序中发生许可错误时，导致的Monkey停止
...（剩余的命令见查看所有命令）
```



- 对58同城进行稳定性测试，执行50次事件，忽略过程中发现的遇到的异常，直到50次事件完成，保存日志

	```java
	adb shell monkey -p com.wuba --throttle 500 --ignore-crashes --ignore-timeouts --ignore-security-exceptions -v-v-v 50 >d:\wuba5.txt
	```

	



#### 设置操作的比例

#### **操作事件简介**

Monkey所执行的随机事件流中包含11大事件。Monkey通过这11大事件来模拟用户的常规操作，对手机App进行稳定性测试。

##### **1.触摸事件**

触摸事件是指在屏幕某处按下并抬起的操作

> 该事件由一组Touch（ACTION_DOWN）和Touch（ACTION_UP）事件组成，在手机上看到实际操作类似于点击。

- 可通过`--pct-touch`参数来配置其事件百分比。



##### **2.手势事件**

手势事件是指在屏幕某处的按下、随机移动、抬起的操作，即直线滑动操作。

> 该事件是由一个ACTION_DOWN事件、一系列ACTION_MOVE事件和一个ACTION_UP事件组成的，
>
> 在手机上看到的实际操作是一个没有拐弯的直线滑动操作。

- 可通过`--pct-motion`参数来配置其事件百分比。



##### **3.二指缩放事件**

二指缩放事件是指在屏幕上的两处同时按下，并同时移动，最后同时抬起的操作，即智能机上的放大缩小手势操作。

> 该事件起始是一个ACTION_DOWN事件和一个ACTION_POINTER_DOWN事件，即模拟两个手指同时点下；
>
> 中间是一系列的ACTION_MOVE事件，即两个手指同时在屏幕上直线滑动；
>
> 结束是由一个ACTION_POINTER_UP事件和一个ACTION_UP事件组成的，即两个手指同时放开。

可通过`--pct-pinchzoom`参数来配置其事件百分比。



##### **4.轨迹事件**

轨迹事件是由一个或多个随机的移动组成的，有时会伴随着点击。

很早之前的Android手机带有轨迹球，这个事件就是模拟的轨迹球的操作。

现在的手机几乎都没有轨迹球，但轨迹球事件中包含曲线滑动操作，如果被测程序需要曲线滑动时可以选用此参数。

>该事件是由一系列的Trackball（ACTION_MOVE）事件组成的，观察手机上的操作，即为一系列的曲线滑动操作。 

- 可通过`--pct-trackball`参数来配置其事件百分比。



##### **5.屏幕旋转事件**

屏幕旋转事件是一个隐藏事件，在Android官方文档中并没有记录这个事件。它其实是模拟的Android手机的横屏和竖屏切换。

> 该事件由一个rotation事件组成，其中degree表示的是旋转方向，顺时针旋转，0表示旋转90度的方向，1表示旋转180度的方向，2表示旋转270度的方向，3表示旋转360度的方向。在执行过程中，可以看到手机屏幕在横竖屏之间不断地切换。

- 可通过`--pct-rotation`参数来配置其事件百分比。



##### **6.基本导航事件**

基本导航事件是指点击方向输入设备的上、下、左、右按键的操作，现在手机上很少有上、下、左、右按键，这种事件一般用得比较少。

> 该事件是由一个Key（ACTION_DOWN）和一个Key（ACTION_UP）组成的，点击的就是上、下、左、右四个方向按键。

- 可通过`--pct-nav`参数来配置其事件百分比。



##### **7.主要导航事件**

主要导航事件是指点击“主要导航”按键的操作，这些按键通常会导致UI界面中的动作，如键盘的中间键、回退按键、菜单按键。

> 该事件是由一个Key（ACTION_DOWN）和一个Key（ACTION_UP）组成的，点击的按键就是中间键和菜单键。

- 可通过`--pct-majornav`参数来配置其事件百分比。



##### **8.系统按键事件**

系统按键事件是指点击系统保留使用的按键的操作，如点击Home键、返回键、音量调节键等。

从Monkey执行该事件对外输出的日志可以看到：

> 该事件是由一个Key（ACTION_DOWN）和一个Key（ACTION_UP）组成的，点击的就是上面说到的几个系统按键。

- 可通过`--pct-syskeys`参数来配置其事件百分比。



##### **9.启动Activity事件**



启动Activity事件是指在手机上启动一个Activity的操作。在随机的时间间隔中，Monkey将执行一个`startActivity()`方法，作为最大限度上覆盖被测包中全部Activity的一种方法。

> 该事件是由一个Switch操作组成的，从手机上看，上面的操作实际是打开了`com.android.settings`这个应用的一个`com.android.settings.Settings`的Activity界面。

- 可通过`--pct-appswitch`参数来配置其事件百分比。



##### **10.键盘事件**

键盘事件主要是一些与键盘相关的操作。比如点击输入框、键盘弹起、点击输入框以外区域、键盘收回等。

> 从Monkey执行该事件对外输出的日志可以看到： 如日志所示，这里主要是键盘的打开和关闭操作。

- 可通过`--pct-flip`参数来配置其事件百分比。



##### **11.其他类型事件**

其他类型事件包括了除前面提到的10种事件外其他所有的事件，如按键、其他不常用的设备上的按钮等。

> 该事件是由一个Key（ACTION_DOWN）和一个Key（ACTION_UP）组成的，点击的按键就是其他的一些系统按键，如字母按键、数字按键等。因为现在手机很少带字母按键或数字按键，所以这个事件一般使用得比较少。

可通过`--pct-anyevent`参数来配置其事件百分比。

```java
--pct-touch			//1.触摸事件
--pct-motion		//2.动作事件（手势）
--pct-pinchzoom		//3.二指缩放事件
--pct-trackball 	//4.轨迹事件
--pct-rotation    	//5.屏幕旋转事件
--pct-nav    		//6.基本导航事件
--pct-majornav		//7.主要导航事件。
--pct-syskeys    	//8.系统按键事件
--pct-appswitch		//9.启动activity事件    
--pct-flip			//10.键盘事件
```





## 4、实战执行示例

 

模拟用户行为，设置不同操作的比例，执行58同城稳定性测试10个小时，并生成报告

```java
adb shell monkey -p com.wuba --throttle 1000 --ignore-crashes --ignore-timeouts --ignore-security-exceptions -v-v-v 3600 >d:\wuba5.txt
```















# EX-1、何为adb

> `apk`：安卓应用安装包
>
> `sdk`：
>
> - SDK 就是 Software Development Kit 的缩写，翻译过来——软件开发工具包。
>
> - 这是一个覆盖面相当广泛的名词，辅助开发某一类软件的相关文档、范例和工具的集合都可以叫做SDK。

- `adb`（Android Debug Bridge）是 Android `sdk` 的一个工具

- `adb`是用来链接<font color='red'>安卓手机</font>和PC端的桥梁，要有`adb`作为二者之间的维系，才能让用户在电脑上对手机进行全面的操作

- Android的初衷是用`adb`这样的一个工具来协助开发人员在开发Android应用的过程中，更快更好的调试apk。

  因此，`adb`具有安装卸载`apk`、拷贝推送文件、查看设备硬件信息、查看应用程序占用资源、在设备执行Shell命令等功能

  

**`adb`的组成**

- 客户端`client`：运行在你的电脑上，可以通过adb命令调用起一个客户端
- 服务器`server`：运行在电脑的后台，负责管理`client`和`daemon`进行通信
- 守护进程`daemon`：运行在模拟器或者Android设备的后台