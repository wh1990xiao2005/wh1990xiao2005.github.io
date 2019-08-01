---
title: Android平台下使用ormLite的一个小Tip
date: 2017-04-06 10:19:10
tags: Android
categories: 编程世界
---

相信不少人在实际开发中使用一些框架，在数据库这方面，有一些成熟的框架。比如greenDao，ormLite之类。   
最近我在实际开发中，使用了ormLite。使用方法就不在赘述了，这个诸位去谷歌、百度一下就可发现，或者去官网也可。   
这里给大家一个小提醒，在使用ormLite进行数据库存取访问时，dao类尽量使用单例。  
我之前的写法如下：    

```
public AvatarStoreDao(Context context) {
    this.context = context;
    try {
        dbHelper = DatabaseHelper
            .getHelper(context, number, true);
        avatarStoresDao = dbHelper.getDao(AvatarStore.class);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

这种写法会在每次使用前都new一个新的AvatarStoreDao对象，因此上面的构造方法也会跟着跑一次。当数据访问过于频繁的时候，某些机器容易被卡黑屏。    
解决方法如下： 

```
public AvatarStoreDao(Context context, String number) {
    this.context = context;
    try {
        dbHelper = DatabaseHelper.getHelper(context, number, true);
        avatarStoresDao = dbHelper.getDao(AvatarStore.class);
    } catch (Exception e) {
        e.printStackTrace();
    }
}

public static AvatarStoreDao getInstance(Context context, String number) {
    if (instance == null) {
        instance = new AvatarStoreDao(context, number);
    }
    return instance;
}
```

这样在使用前，需要调用getInstance方法，这样一来，仅当instance为null的时候才会走构造方法，可以提升一下使用的流畅性。