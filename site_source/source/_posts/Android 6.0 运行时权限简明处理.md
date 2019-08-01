---
title: Android 6.0 运行时权限简明处理
date: 2016-12-19 17:15:00
tags: Android
categories: 编程世界
---

今天来跟大家聊这样一个话题——权限。  
正如各位所知，传统意义上的权限是在用户安装APP时被许可的。尽管在使用过程中，某些第三方Rom或者安全软件会再次提示权限，但从系统本身来说，就是被放行了。  
在Google推出6.0之后，引入了新的权限机制，叫做运行时权限（Runtime Permission）。该机制限制某些敏感的权限，比如读写外置存储，访问隐私数据等等。对用户的数据保护提供良好的保障，但同时也为开发者提供了一些绊脚石。  
那么作为一名开发者，应该如何做呢？  
首先就是端正心态，这样做确实是对用户有好处的。  
然后就是学习了，也就是本篇文章的正题。  
我们按照Android studio的新建项目向导建立新的空项目后，按照原先的方法在Androidmanifest.xml做权限声明，如下：  

```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

然后在Activity中尝试新建一个文件，如下：  

```
File newFile = new File(Environment.getExternalStorageDirectory() + File.separator + "test.txt");
if (newFile.exists() && newFile.isFile()) {
    newFile.delete();
}
try {
    newFile.createNewFile();
    Toast.makeText(this, "文件创建成功", Toast.LENGTH_SHORT).show();
} catch (IOException e) {
    e.printStackTrace();
    Toast.makeText(this, "文件创建失败", Toast.LENGTH_SHORT).show();
}
```

然后运行，如果你的手机是基于Android 6.0，你就会发现，文件根本没有办法被创建。会报Permission denied 错误，意思就是权限禁止。  
解决的方法也很简单，分两步走，第一步先授权，第二步就是在授权响应的回调接口中获取到授权是否成功的值。  
授权的方法可参考如下代码片： 

```
private void checkStoragePermission() {
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
        //未授权，请求授权
        ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE}, REQ_PERMISSION_STORAGE);
    }
}
```

此处有检查，如果未经授权，则去授权。  
授权响应回调中的处理可参考如下代码片：  

```
if (requestCode == REQ_PERMISSION_STORAGE) {
    if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
        Toast.makeText(this, "得到授权", Toast.LENGTH_SHORT).show();
    } else {
        Toast.makeText(this, "授权取消", Toast.LENGTH_SHORT).show();
    }
}
```    

各位可在Toast处按照项目需求做出具体的适合自己项目的处理。  
上述完整的代码已经放到Github上面，有兴趣的朋友可以下载查看，网址：https://github.com/XiaoWenHan/AndroidRunTimePermissionDemo/  
下面附上需要做类似处理的权限列表： 

 - 身体传感器
 - 日历
 - 摄像头
 - 通讯录
 - 地理位置
 - 麦克风
 - 电话
 - 短信
 - 存储空间 

给大家提个醒，如果你项目中的targetSdk<23，是无需做上述处理的。  
目前，针对运行时授权处理的框架已经有一些了，感兴趣的朋友可以去看看。下面放上几个：  

 - PermissionsDispatcher
使用标注的方式，动态生成类处理运行时权限，目前还不支持嵌套Fragment。

 - RxPermissions
基于RxJava的运行时权限检测框架

 - Grant
简化运行时权限的处理，比较灵活

 - android-RuntimePermissions
Google官方的例子  

最后，放上一个无需权限处理即可使用的权限：  

> android.permission.ACCESS_LOCATION_EXTRA_COMMANDS
android.permission.ACCESS_NETWORK_STATE
android.permission.ACCESS_NOTIFICATION_POLICY
android.permission.ACCESS_WIFI_STATE
android.permission.ACCESS_WIMAX_STATE
android.permission.BLUETOOTH
android.permission.BLUETOOTH_ADMIN
android.permission.BROADCAST_STICKY
android.permission.CHANGE_NETWORK_STATE
android.permission.CHANGE_WIFI_MULTICAST_STATE
android.permission.CHANGE_WIFI_STATE
android.permission.CHANGE_WIMAX_STATE
android.permission.DISABLE_KEYGUARD
android.permission.EXPAND_STATUS_BAR
android.permission.FLASHLIGHT
android.permission.GET_ACCOUNTS
android.permission.GET_PACKAGE_SIZE
android.permission.INTERNET
android.permission.KILL_BACKGROUND_PROCESSES
android.permission.MODIFY_AUDIO_SETTINGS
android.permission.NFC
android.permission.READ_SYNC_SETTINGS
android.permission.READ_SYNC_STATS
android.permission.RECEIVE_BOOT_COMPLETED
android.permission.REORDER_TASKS
android.permission.REQUEST_INSTALL_PACKAGES
android.permission.SET_TIME_ZONE
android.permission.SET_WALLPAPER
android.permission.SET_WALLPAPER_HINTS
android.permission.SUBSCRIBED_FEEDS_READ
android.permission.TRANSMIT_IR
android.permission.USE_FINGERPRINT
android.permission.VIBRATE
android.permission.WAKE_LOCK
android.permission.WRITE_SYNC_SETTINGS
com.android.alarm.permission.SET_ALARM
com.android.launcher.permission.INSTALL_SHORTCUT
com.android.launcher.permission.UNINSTALL_SHORTCUT