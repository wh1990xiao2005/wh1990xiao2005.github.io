---
title: Mapbox 地图SDK极速集成指导
date: 2016-12-22 11:42:14
tags: Android
categories: 编程世界
---
这次跟各位分享一个地图的SDK。先说说为什么拿Mapbox来用吧。  
首先就是——国际化。当需求要显示国外的地理位置信息的时候，通常使用的是Google Map。但是Google Map目前需要手机带有完整的Google Play套件才行，但是基本上所有的国内上市的手机都把Google Play套件阉割了。然后就是偏移量，从目前来看，Mapbox的路网和卫星照片是重合的，而Google Map则是有一定的偏移，尤其是在中国。最后呢，Mapbox的集成方式实在是太简单了。不过呢，说了这么多优点，再说一个缺点。就是Mapbox的卫星照片略慢，尤其是中国，某些地方还停留在几年前的样子，这个以年为单位，一点都不夸张。  
如果你需要做海外的地图定位，个人认为，Mapbox可作为首选。  
下面说一下具体的集成方法。  
首先，需要去官网注册一个账号，这里附上官网地址链接：  
https://www.mapbox.com/  
注册完成后，进入网页Studio界面，我们会轻松找到Access Token。我们后面会用到。  
然后新建一个Android项目，这里推荐targetAPI<23，但是必须大于15。  
在app目录下的build.gradle文件中添加下面的内容，用来导入依赖库。  

```
repositories{mavenCentral()}
compile('com.mapbox.mapboxsdk:mapbox-android-sdk:4.2.0@aar') {
    transitive = true
}
```  

这里注意版本，虽然复制粘贴过去没有问题，但是依赖库的版本还在不断更新，也许下一秒就发布4.2.1或者其他什么版本，所以这个还是有必要直接去官网上看一下。网址在此：  
https://www.mapbox.com/android-sdk/   
下一步我们在布局文件中添加地图控件，很简单，最简易的写法可以像下面这样：  

```
<com.mapbox.mapboxsdk.maps.MapView
    android:id="@+id/mapview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

当然，我们需要显示一个自己比较熟悉的位置，而且还要能够看得足够清晰，可以写成像下面这样：  

```
<com.mapbox.mapboxsdk.maps.MapView
    android:id="@+id/mapview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    mapbox:center_latitude="39.069"
    mapbox:center_longitude="117.221"
    mapbox:style_url="@string/style_satellite_streets"
    mapbox:zoom="15" />
```

这里提醒一下，在xml最上方加上命名空间，否则会报错：  

```
xmlns:mapbox="http://schemas.android.com/apk/res-auto"  
```        

这样做，可以同时显示路网和卫星照片，虽然卫星照片可能比较过时，但是确实很清楚。而且可以稍微移动一下，看看国外的风景，情况就大不一样了。  
下一步需要在Activity中完成初始化等操作，在onCreate()方法中添加如下代码：  

```
setContentView(R.layout.activity_main);
MapboxAccountManager.start(this, getString(R.string.access_token));
mv = (MapView) findViewById(R.id.mapview);
mv.onCreate(savedInstanceState);
mv.getMapAsync(new OnMapReadyCallback() {
    @Override
    public void onMapReady(MapboxMap mapboxMap) {

    }
});
```

当然，这只是个例子。  
这里要注意的就是必须在Activity中的各个生命周期中添加相应的方法，比如onResume()中，需要加上：  

```
mv.onResume();
```

其他生命周期回调类似。  
最后在AndroidManifest.xml中做好相关权限声明：  

```
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.INTERNET" />
```

还有一个Service：  

```
<service android:name="com.mapbox.mapboxsdk.telemetry.TelemetryService" />
```

该Service在Application节点内。  
好了，运行一下吧，一切顺利的话就可以看到地图的显示了。    

下面附上完整的项目源码：  
https://github.com/XiaoWenHan/MapboxAndroidDemo  