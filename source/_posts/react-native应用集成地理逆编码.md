---
title: react-native应用集成地理逆编码
date: 2018-06-24 19:13:17
tags: react-native
--- 

##### 一、背景   

   最近，在项目中需要集成定位功能，在网上搜集资料之后，决定采用逆地理定位功能进行实现，那么让我们看看逆地理定位功能是怎么实现
   的。
   
##### 二、思路   
  主要过程分为两部分:    
    (1):一是获取定位点的经纬度;    
    (2):二是使用一步骤获取的经纬度，进行调用能够解析经纬度的接口API，进行对应地理信息的获取.     
  在解决上述问题的过程中使用了`百度逆地理`和`高德逆地理`定位功能，如下所示分别是高德逆地理和百度逆地理的访问文档 
  
  [高德地理/逆地理编码文档地址](http://lbs.amap.com/api/webservice/guide/api/georegeo)   
  [百度地理/逆地理解析文档地址](https://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-geocoding-abroad)   
  
##### 三、解决方法
 首先要解决的是公共问题，也就是经纬度的获取，在这里，我们使用react-native提供的接口[Geolocation](https://facebook.github.io/react-native/docs/geolocation.html)进行获取，
 说到这里，我们就得去了解一下Geolocation是什么东西？
 ###### 1.Geolocation信息说明
 Geolocation就是react-native官方提供的用于提供基本定位信息和经纬度信息的API，使用Geolocation时需要获取相应的系统权限   
 （1）权限说明    
 - IOS系统：   
 需要在Info.plist中增加`NSLocationWhenInUseUsageDescription`字段来启用定位功能。如果你使用react-native init创建项目，定位会被默认启用。  
 - Android系统：   
 需要请求访问地理位置的权限，你需要在AndroidManifest.xml文件中加入如下一行：
 
  ```js
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
  ```
  
  （2）方法
 
 + static requestAuthorization()    
  根据pList上配置的密钥请求合适的位置权限。
  如果设置NSLocationAlwaysUsageDescription，它将请求始终授权，但如果设置NSLocationWhenInUseUsageDescription，它将请求InUse授权。
 + static getCurrentPosition(geo_success: Function, geo_error?: Function, geo_options?: GeoOptions)         
  成功时会调用geo_success回调，参数中包含最新的位置信息。     
  支持的选项：timeout (ms), maximumAge (ms), enableHighAccuracy (bool)    
 选项的含义如下所示：     
     + timeout：指定获取地理位置时的超时时间，默认不限时
     + maximumAge：最长有效期，此参数用来指定多久再次获取位置
     + enableHighAccuracy：指示浏览器获取高精度的位置，默认为false，开启后浏览器可能花费更长的时间获取更精确的位置数据    
 对于该方法请求成功之后，会有下面返回结果：
  - 1.经度：coords.longitude
  - 2.纬度：coords.latitude
  - 3.精确度：coords.accurary
  - 4.海拔：coords.altitude
  - 5.海拔准确度：coords.altitudeAcurary
  - 6.行进方向：coords.heading
  - 7.地面速度：coords.speed
  - 8.时间戳：new Date(position.timestamp)      
 当然，我们也可能遇到请求失败的问题，那么，请求失败都有下面这几种情况     
  1.用户拒绝定位、2.请求超时、3.暂时获取不到定位信息、4.未知错误
 + static watchPosition(success: Function, error?: Function, options?: GeoOptions)      
   持续监听位置，每当位置变化之后都调用success回调。     
   支持的选项：timeout (ms), maximumAge (ms), enableHighAccuracy (bool), useSignificantChanges (bool)     
 
 + static clearWatch(watchID: number)       
 清除位置监听，位置监听id可由watchPosition方法返回
 + static stopObserving()       
 
 【注意】：我在这里使用的react-native的版本是0.51.0版本，最新版本是0.56.0版本，如果你使用的是最新版本，请参考最新版写法 
 
 如下面代码，我们能够进行获取到经纬度
 ```js
 
  componentDidMount() {
    this._getLocation();
    this.watchPosition = this._getWatchPosition();
  }

  componentWillUnmount() {
    navigator.geolocation.clearWatch(this.watchPosition);
  }

  _getLocation = () => {
    navigator.geolocation.getCurrentPosition(
      (initialPosition) => this.setState({ initialPosition }, () => {
        this._getAddress();
      }),
      (error) => alert(error.message),
      { enableHighAccuracy: true, timeout: 20000, maximumAge: 1000 }
    );
  };

  _getWatchPosition = () => {
    return navigator.geolocation.watchPosition(
      (lastPosition) => {
        this.setState({ lastPosition }, () => { this._getAddress(); });
      },
      (error) => alert(error.message),
      { enableHighAccuracy: true, timeout: 20000, maximumAge: 1000 }
    );
  };
  
```
其中`initialPosition`和`lastPosition`的对象内容一直，只不过一个是初始化的时候的经纬度信息，一个是当前最新的经纬度信息，其返回结果如下所示：   
<center>
<img src="https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/react-native-locations-geolocation-result.png?raw=true" width="45%" height="35%" />
geolocation数据结构
</center>

注意：在上面代码中，navigator.geolocation指的不是导航器，而是Geolocation的一个类，能够获取到定位的经纬度信息
 我分别使用代码，进行了两种api的调用，如下代码：
###### 2.高德逆地理解析
在高德逆地理解析中，需要首先进行获取高德账号Key。  
参考下面文章即可：[高德key的申请及使用说明](http://lbs.amap.com/api/webservice/guide/api/georegeo) 
创建界面如下所示，在创建时我们选择`web服务`选项：
![amap](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/api-create-amap.png?raw=true)
在获取key之后，我们就可以说是所有要准备的东西都已经准备好了，也就是有了Geolocation获取的经纬度，也有了能够正常访问的逆地理解析API，那我们就将
经纬度进行介绍，
首先我们进行判断api能否使用，通过我们手动拼接URL，然后在浏览器地址栏输入，如下界面即为正常
+ 1.输入的url
```js
https://restapi.amap.com/v3/geocode/regeo?key=高德申请的key&poitype=all&radius=3000&output=json&extensions=all&roadlevel=0&location=120.19698604,30.11286062
```
注意：上面的key需要进行修改为自己的在高德申请的key，才能够进行访问到数据
+ 2.回车之后获取的数据界面
![请求结果数据展示](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/react-native-locations-amap-result.png?raw=true)

 `#注意#：如上所示，能够通过浏览器访问到数据，则说明接口可以正常使用，也就是我们可以通过自己的请求进行请求数据了` 

+ 3.如下代码所示为完整请求过程
 ```js
_getAddress = () => {
    // 经度：positionData.longitude
    // 纬度：positionData.latitude
    // 最后一步 todo：高德逆地理编码转
    // lat<纬度>,lng<经度>

    const aMapLocationConfig = {
      key: '这里填写你自己申请的key，我的就不让你用了',
      poitype: 'all',
      radius: 3000,
      output: 'json',
      extensions: 'all',
      roadlevel: 0,
    };
    const aMapLocationURL = 'https://restapi.amap.com/v3/geocode/regeo'; //GET请求

    // callback=renderReverse&location=35.658651,139.745415&output=json&pois=1&ak=您的ak
    // radius=3000&output=json&extensions=all&roadlevel=all&location=30.11286062,120.19698604
    // ?output=xml&location=116.310003,39.991957&key=<用户的key>&radius=1000&extensions=all
    const { lastPosition } = this.state;
    if (!lastPosition) return null;
    const { longitude, latitude } = lastPosition.coords;
    const location = longitude + ',' + latitude;
    const aMapUrl = aMapLocationURL + '?' + Qs.stringify({ ...aMapLocationConfig }) + '&location=' + location;

    InteractionManager.runAfterInteractions(() => {
      gaxios(aMapUrl)
        .then((response) => {
          this.setState({
            address: response
          }, () => {
          });
        }).catch((error) => {
          console.log('获取地址信息出错' + error);
        });
    });
  };
```
+ 4.高德API请求参数和响应参数说明        
   可参考官方文档---[https://lbs.amap.com/api/webservice/guide/api/georegeo#regeo](https://lbs.amap.com/api/webservice/guide/api/georegeo#regeo)
###### 3.百度逆地理解析
同样的，对于百度逆地理解析也需要进行获取key值，如下面文档说明[百度逆地理解析的申请及说明文档](https://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-geocoding-abroad)
按照说明文档中的，我们需要进行[创建应用](https://lbsyun.baidu.com/apiconsole/key/create)   
创建界面如下所示：
![baidu](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/api-create-baidu.png?raw=true)
在创建应用时，需要注意的是应该创建应用类型为`服务端`的类型，然后我们就能够进行请求api获取逆地理解析的详细信息了，如下所示：

首先我们进行判断api能否使用，通过我们手动拼接URL，然后在浏览器地址栏输入，如下界面即为正常
+ 1.输入的url  
```js
http://api.map.baidu.com/geocoder/v2/?location=30.1128606254,120.1969860408&output=json&pois=1&ak=百度申请的ak

```
注意：上面的ak需要进行修改为自己的ak，才能够进行访问到数据，同时，为了能够获取到数据，先将ip白名单设置为`0.0.0.0/0`,如下所示：
![url地址](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/white_ip.png?raw=true)
+ 2.回车之后获取的数据界面     
![结果数据](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/url_result.png?raw=true)
 
 `#注意#：如上所示，能够通过浏览器访问到数据，则说明接口可以正常使用，也就是我们可以通过自己的请求进行请求数据了`    
 
+ 3.如下为完整请求过程       

```js
   _getAddress = () => {
     // 经度：positionData.longitude
     // 纬度：positionData.latitude
     // 最后一步 todo：百度地图逆地理编码转
     // lat<纬度>,lng<经度>
 
     const baiduLocationConfig = {
       // callback: 'renderReverse',
       ak: '这里填写你自己申请的ak，我的就不让你用了',
       pois: 1,
       output: 'json',
       latest_admin: 1,
       language_auto: 1,
       extensions_town: true,
       extensions_road: true,
       radius: 1000,
     };
     const baiduLocationURL = 'https://api.map.baidu.com/geocoder/v2/'; //GET请求
     // callback=renderReverse&location=35.658651,139.745415&output=json&pois=1&ak=您的ak
 
     const { lastPosition } = this.state;
     if (!lastPosition) return null;
     const { longitude, latitude } = lastPosition && lastPosition.coords;
     const location = latitude + ',' + longitude;
     const baiduUrl = baiduLocationURL + '?' + Qs.stringify({ ...baiduLocationConfig }) + '&location=' + location;
 
     InteractionManager.runAfterInteractions(() => {
       gaxios(baiduUrl)
         .then((response) => {
           this.setState({
             address: response
           }, () => {
           });
         }).catch((err) => {
           console.log('获取地址信息出错', err);
         });
     });
   };

```
+ 4.百度API请求参数和响应参数说明                
 可参考官方文档--[http://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-geocoding-abroad](http://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-geocoding-abroad)
##### 四、效果展示
发送请求之后，我们能够看到如下效果，在请求之后，我将请求到的数据进行了分类，进行对其中具体的字段进行了说明：
###### 1.高德地图请求效果
![高德效果展示](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/react-native-locations-amap.gif?raw=true)
###### 2.百度地图请求效果
![百度效果展示](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-locations/react-native-locations-baidu.gif?raw=true)

##### 五 演示代码github地址
GitHub地址：[https://github.com/suwu150/react-native-locations](https://github.com/suwu150/react-native-locations)
，能够直接通过`git clone`进行克隆运行，运行命令如下所示：
1.克隆项目
 ```shell
 git clone https://github.com/suwu150/react-native-locations.git
```
2.进入项目，安装依赖库文件
```shell
cd react-native-locations
npm install
```
3.运行项目
  ios：
  ```js
 react-native run-ios
```

 Android:
 ```js
 react-native run-android
```
注意：在运行安卓项目的时候，必须手动打开模拟器  

##### 六 可能出现的问题汇总
+ 在安卓中会出现下面问题，返回错误信息
 // message: "No location provider available.", code: 2
 // message: "Geolocation timed out.", code: 3
其中2代代表未开启定位服务，3代表请求定位超时
+ 在ios系统中同样会有这种问题，如下面提示信息
```javascript
  if (Platform.OS === 'ios') {
  if (error && error.code === 1) {
    Alert.alert('请允许该应用访问位置服务', '', [{ text: '好', onPress: this.closeModal }]);
  } else if (error && error.code === 2) {
    Alert.alert('位置信息GPS服务不可用', '', [{ text: '好', onPress: this.closeModal }]);
  } else if (error && error.code === 3) {
    Alert.alert('获取GPS信息超时，请重新打开该页面获取信息', '', [{ text: '好', onPress: this.closeModal }]);
  } else {
    Alert.alert('未知错误', '', [{ text: '好', onPress: this.closeModal }]);
  }
```

---------   


