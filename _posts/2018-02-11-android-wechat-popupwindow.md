---
title: 高仿微信聊天界面长按弹框样式
---
![](http://upload-images.jianshu.io/upload_images/5361063-51d2b77379dbc87e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 效果图
![](http://upload-images.jianshu.io/upload_images/5361063-9b6576d528bccad6.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 背景
在公司做的项目里面，刚好有需要用到微信聊天界面长按弹框样式这种UI的。

网上找了一下，没找到。

Android现成的 [ListPopupWindow](https://developer.android.com/reference/android/widget/ListPopupWindow.html)又不能满足需求。

因此在非上班时间撸一个出来，供大家使用。

###  示例代码
关键文件、示例代码及使用方法等我都放到github上了。

这篇文章就不展开说明了。

这篇文章重点说明下怎么把这个样式给撸出来的。

说明一些关键点。

**让大家知其然，更加知其所以然。**

[代码传送门](https://github.com/nesger/WechatPopupWindow)

### 关键代码解剖

##### PopupWindow默认宽高设置

我们知道，创建PopupWindow时可以指定显示的宽度和高度。

这边说下代码里面默认的宽高是如何设置的。

**默认宽度＝设备的宽度/3。**

默认高度的设置就比较麻烦一点。

因为高度如果设置过少，可能显示的列表Item数目就会比较少。

如果设置过大，可能显示列表会撑满整个屏幕。

基于此，这边代码设置的默认高度是动态变化的。

变化算法如下：

>最大默认高度＝设备的高度/2。  
列表高度＝Item高度*Item数目。  
**如果列表高度<最大默认高度，默认高度＝列表高度。**  
**如果列表高度>最大默认高度，默认高度＝最大默认高度。**  

至于Item高度如何计算，示例代码里面有，这边就不赘余阐释了。

##### PopupWindow显示位置确定

在说明显示位置之前，我们先来简单说下android的坐标系。

![](http://upload-images.jianshu.io/upload_images/5361063-dbdcdfaa3d07c92c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面的图片简单画了一下，主要是说明

**X轴方向，往右边X值变大。**
**Y轴方向，往下面Y值变大。**

这个对于后面显示位置的计算有用。

接下来我们说下显示位置的，既然是一个位置，那么就会有两个维度值。

X轴（水平方向）

Y轴（竖直方向）

除了维度值，还有一个基准点，这里的基准点就是列表的左上角。

![](http://upload-images.jianshu.io/upload_images/5361063-022c3070cca3b2ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


为了说明该PopupWindow适应各个位置，所以效果图里面有三个按钮，分别位于左下角，中间，右上角。



**X坐标位置确定**

![](http://upload-images.jianshu.io/upload_images/5361063-a37b0adb6b0a0030.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由上图我们可以得出结论。

>**当显示的View在左边时，显示的X坐标=View的中心点X坐标。**  
**当显示的View在右边时，显示的X坐标=View的中心点X坐标-PopupWindow宽度。**  

**Y坐标位置确定**

![](http://upload-images.jianshu.io/upload_images/5361063-fc65270941b53784.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由上图我们可以得出结论。

>**当显示的View在上边时，显示的Y坐标=View的中心点Y坐标。**  
**当显示的View在下边时，显示的Y坐标=View的中心点Y坐标-PopupWindow高度。**  

好了，到此**高仿微信聊天界面长按弹框样式**的代码剖析就结束了。

有任何问题欢迎评论留言。







