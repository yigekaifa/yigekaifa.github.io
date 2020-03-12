---
title: Android中几种定位方式的粗解
date: 2020-03-10 22:46:16
tags: [Android,Location,定位]
categories: Dev
---

之前朋友问我Tiktok为什么禁用了定位，开启了全局代理，还是可以获取到位置。当时想法只有一个：基站定位。我让他在设置里关掉移动服务（关闭手机卡）之后，确实可以正常使用了。

这里简单说一下Android中集中定位方式。
<!--more-->

> 这里是在Android10添加的新权限，在后台获取位置：https://developer.android.com/reference/android/Manifest.permission.html#ACCESS_BACKGROUND_LOCATION

## 1. GPS

权限（注意版本变化）：

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

具体定位代码

```java
var locManager = getSystemService(Context.LOCATION_SERVICE) as LocationManager
var loc = locManager.getLastKnownLocation(LocationManager.GPS_PROVIDER)
if(loc != null){
    Log.e("gpslocation", loc.toString())
    toast(loc.toString())
}
locManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0F, object : LocationListener{
    override fun onStatusChanged(provider: String?, status: Int, extras: Bundle?) {
 
    }
 
    override fun onProviderEnabled(provider: String?) {
    }
 
    override fun onProviderDisabled(provider: String?) {
    }
 
    override fun onLocationChanged(location: Location?) {
        Log.e("gpslocation", location.toString())
        toast(location.toString())
    }
})
```

### 2. Network获取位置

与GPS定位相同，代码都一样。只是申请权限不同。并且在室内时，GPS需要一定时间来定位，而Network在网络状况不好时不可用。

### 3. IP

APP通过对当前IP的分析，可以得到你大致的位置。一般不采用这种方案，IP太容易被伪装。

### 4. WiFi（WLAN）定位

需要权限：

```xml
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
```

这个也比较简单，就是过程就是拿到当前的WiFi信息，然后通过mac地址（BSSID）进行查询。具体查询的接口可以去聚合数据搜索一下，这里不再多说。

### 5. 基站定位

这个要稍微说一下。在你关闭了所有定位传感器之后，如果你的手机还是处于有服务的状态下，就可以进行大概定位。通过TelephonyManager获取当前手机信号的基站信息。可以定位到大概区以下的位置。

```
MCC，Mobile Country Code，移动国家代码（中国的为460）
MNC，Mobile Network Code，移动网络号码（中国移动为00，中国联通为01）
LAC，Location Area Code，位置区域码
CID，Cell Identity，基站编号，是个16位的数据（范围是0到65535）
```

看似类型繁多，但是实际用起来用处不是很多。建议大家还是用第三方定位库吧，简单精准还好用。

参考文章
> https://developer.android.com/guide/topics/location/strategies
