---
title: react-native打包Android应用问题及安装汇总
date: 2018-07-10 09:36:34
tags: Android
---
##### 1. 应用未安装
  + 问题：在安装apk安装包的时候，会出现`应用未安装`的提示
  + 解决方法： 删掉手机上已经安装过的apk，进行重新安装即可
  + 原因分析： 是由于签名问题，在程序中包名一致，但是签名不同就会出现不能安装问题
  
##### 2. 很抱歉，程序出现异常，即将退出
  + 问题：在打开程序的时候，会出现`很抱歉，程序出现异常，即将退出`的提示，然后退出程序
  + 现象：在android日志中能够看到下面日志：
  ```txt
        07-10 19:05:07.446 12390-12436/? E/ReactNativeJS: undefined is not an object (evaluating 's.View.propTypes.style')
        07-10 19:05:07.455 12390-12436/? E/ReactNativeJS: Application Lova has not been registered.
        
            Hint: This error often happens when you're running the packager (local dev server) from a wrong folder. For example you have multiple apps and the packager is still running for the app you were working on before.
            If this is the case, simply kill the old packager instance (e.g. close the packager terminal window) and start the packager in the correct app folder (e.g. cd into app folder and run 'npm start').
        
            This error can also happen due to a require() error during initialization or failure to call AppRegistry.registerComponent.
        07-10 19:05:08.012 1003-1271/? E/WifiStateMachine:  ConnectedState (when=-5ms what=131155 arg1=5!CMD_RSSI_POLL 5 0 "11223344" ac:c1:ee:3c:82:dd rssi=-51 f=2412 sc=60 link=65 tx=0.5, 0.0, 0.0  rx=0.3 bcn=0 [on:0 tx:0 rx:0 period:2983] from screen [on:0 period:-2082616660] gl hn u24 rssi=-46 ag=0 hr ticks 0,0,0 ls-=0 [56,56,56,56,61] brc=0 lrc=0
        07-10 19:05:08.015 1003-1271/? E/WifiStateMachine:  L2ConnectedState (when=-8ms what=131155 arg1=5!CMD_RSSI_POLL 5 0 "11223344" ac:c1:ee:3c:82:dd rssi=-51 f=2412 sc=60 link=65 tx=0.5, 0.0, 0.0  rx=0.3 bcn=0 [on:0 tx:0 rx:0 period:3] from screen [on:0 period:-2082616657] gl hn u24 rssi=-46 ag=0 hr ticks 0,0,0 ls-=0 [56,56,56,56,61] brc=0 lrc=0
             get link layer stats 0
```
  + 解决方法：通过修改build.gradle中的文件
   - [https://blog.csdn.net/lxk_1993/article/details/50511172](https://blog.csdn.net/lxk_1993/article/details/50511172)
   - 下面代码中BuildConfig不需要自动导入，会默认进行导入，哪怕android studio报错，也不要进行手动导入------报未注册错误appregister。。。
   ```angularjs
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }
```
  
##### 3.  Android Gradle 3.0.0-alpha2 plugin, Cannot set the value of read-only property 'outputFile'
  + 问题：
  + 解决方法：
  ```js
applicationVariants.all { variant -> 
    variant.outputs.each { output ->
        def SEP = "_"
        def flavor = variant.productFlavors[0].name
        def buildType = 
        variant.variantData.variantConfiguration.buildType.name
        def version = variant.versionName
        def date = new Date()
        def formattedDate = date.format('ddMMyy_HHmm')
        def newApkName = PROJECT_NAME + SEP + flavor + SEP + buildType + SEP + version + SEP + formattedDate + ".apk"
        def file = new File(newApkName)
        output.outputFile = file
    }
}
```
修改为：
Use all() instead of each()
Use outputFileName instead of output.outputFile if you change only file name (that is your case)
Example from the guide:
```js
// If you use each() to iterate through the variant objects,
// you need to start using all(). That's because each() iterates
// through only the objects that already exist during configuration time—
// but those object don't exist at configuration time with the new model.
// However, all() adapts to the new model by picking up object as they are
// added during execution.
android.applicationVariants.all { variant ->
    variant.outputs.all {
        outputFileName = "${variant.name}-${variant.versionName}.apk"
    }
}
```
  + 原因分析： 版本更新导致的问题
  