---
title: android调试之无线连接设备 
---
[简书地址](http://www.jianshu.com/p/ce92c0ee132f){:target="_blank"}
<br/><br/><br/>
![](http://upload-images.jianshu.io/upload_images/5361063-3fd7a77e39fb3da7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 背景
在用mac pro开发时，mac上面的USB插槽就两个，一个接了鼠标，一个接了键盘，然后，然后就没了，那我真机调试时肿么办？

难道每次都要拔掉鼠标，调试完之后又接回鼠标，如此循环往复？

![](http://upload-images.jianshu.io/upload_images/5361063-88747d7fcfa68302.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因此无线连接进行真机调试的方法就迫在眉睫。

### 装备
- USB线（第一次需要使用）
- 同一个Wi-Fi局域网
- 一台电脑（mac／windows／linux，这里以mac为例）
- 手机（android）

### 实战
说完背景，准备好装备，接下来就开始实际操作了。

令人窒息的操作其实
>非常简单  
非常简单  
非常简单  

1. 将手机通过USB线连接上mac。

2. 打开mac上的终端程序。（这个大家都懂，就不说了～）

3. 输入命令***adb tcpip 5555***，看到如下提示表明操作成功。
![](http://upload-images.jianshu.io/upload_images/5361063-5f52b8d8dba09803.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 接着输入命令***adb shell netcfg***，在显示的信息中查找wlan0所在的行，会看到一串IP值，比如这里为10.32.33.109。
![](http://upload-images.jianshu.io/upload_images/5361063-bd4ed7b5048a41a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
5. 接着输入命令***adb connect 10.32.33.109:5555***，会提示连接成功。
 然后输入命令***adb devices***查看设备会发现10.32.33.109:5555显示在列表里，拔掉USB后再查看就只剩下我们刚刚配置的设备啦。
![](http://upload-images.jianshu.io/upload_images/5361063-00e5313449c348fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此android调试无线连接设备就大功告成了，你的USB接口可以留给其他需要的设备啦。

### Tips
1. 为了达到更加精简的步骤，可以给手机设置一个静态IP地址。
这样就做到了一次USB线，到处无线的状态。
然后记住IP地址，以后要用的时候如果手机没有连接上，只需要调用***adb connect***命令即可。
2. 其实只要买个集线器就可以完美解决设备插槽不够的问题啦，不过这样就没法~~zhuang B~~，哦，是没法get新技能了～



