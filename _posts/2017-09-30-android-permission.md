---
title: android开发之调皮的权限
---
[简书地址](http://www.jianshu.com/p/8de4385b6ade){:target="_blank"}
## 文章背景
撰写本文的目的在于帮助开发者们解决android中动态申请权限的坑。

在android6.0以前，申请权限的方式很简单，只需要在清单文件AndroidManifest.xml中申请即可。需要用到时系统会弹框提示用户选择需要的权限。

但是android6.0之后，权限需要进行动态申请。而且有些基础权限也需要申请，而这些容易被开发者忽略的权限可能会导致原本正常运行的程序出现各种各样的坑。

***本文就是为了解决这些坑，为了让广大开发者避免掉坑而撰写的***。

## 适用平台
目前适合所有android平台。

## 使用场景
对android6.0以上系统有**大作用**，兼容android6.0以下系统。
毕竟android6.0以上权限才开始变得***调皮***，需要动态申请了。
尤其适合需要用到权限的APP。
特别是之前用户完全没有感知的存储权限的申请更加适用。

## 具体方案
google既然引入了动态权限申请，自然有一套API来处理。
但是google的API不够直观，并且比较基础。

本人参照了一些资料，然后自己写了一个demo，已经开源到github。
大家可以上去观看。欢迎**star、follow、提issue**。
**[PermissionManager](https://github.com/nesger/PermissionManager){:target="_blank"}**

下面说下具体的用法：(*github上也有说明*)
- 拷贝permission包下的所有文件到你的项目中。
- 定义PermissionHelper变量。
 ```
private PermissionHelper permissionHelper;
 ```
- 在需要检查权限的地方调用doPermissionCheck()方法。

 ```
/**
  * 检查是否拥有权限
  */
private void doPermissionCheck(){
    permissionHelper = PermissionHelper.getInstance(this);
    permissionHelper
            .setForceAccepting(true) // default is false. its here so you know that it exists.
            .request(permissionArrays);
}
 ```
这里的permissionArrays就是你要申请的权限的数组，目前支持申请单个权限和多个权限，只需要在这里配置即可。比如示例代码：
 ```
final String[] permissionArrays = {Manifest.permission.WRITE_EXTERNAL_STORAGE,Manifest.permission.READ_SMS};
 ```
- 实现OnPermissionCallback回调接口。
- 在实现的回调方法里面做各自的事情。具体逻辑可以参考github上代码。

 ```
//用户点击允许权限回调
 @Override
    public void onPermissionGranted(@NonNull String[] permissionName) {
    }

    @Override
    public void onPermissionDeclined(@NonNull String[] permissionName) {
    }

 //权限已经打开了
    @Override
    public void onPermissionPreGranted(@NonNull String permissionsName) {
    }

    //需要申请权限
    @Override
    public void onPermissionNeedExplanation(@NonNull String permissionName) {
        permissionHelper.requestAfterExplanation(permissionName);
    }

 //用户点击禁止权限并且不再提示时回调
    @Override
    public void onPermissionReallyDeclined(@NonNull String permissionName) {
    }

    //android6.0以下会触发
    @Override
    public void onNoPermissionNeeded() {
    }

    //记得手动重写这个方法
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        permissionHelper.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }
 ```
github上的代码演示的是一次性强制申请单个或多个权限的用法。
至于非强制性申请以及其他各种场景，各位有需要的可以评论留言或者到github上提issue。
有任何想法欢迎交流。
演示截图如下：
![权限申请](http://upload-images.jianshu.io/upload_images/5361063-2c3f0c5ba403f61f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


