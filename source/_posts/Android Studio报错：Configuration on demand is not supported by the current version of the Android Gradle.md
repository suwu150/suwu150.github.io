---
title: Android Studio报错：Configuration on demand is not supported by the current version of the Android Gradle
date: 2018-07-09 14:28:21
tags: Android
---

#### Android Studio报错：Configuration on demand is not supported by the current version of the Android Gradle
##### 1.错误
Configuration on demand is not supported by the current version of the Android Gradle plugin since you are using Gradle version 4.6 or above.?
Suggestion: disable configuration on demand by setting org.gradle.configureondemand=false in your gradle.properties file or use a Gradle version less than 4.6.

##### 2.翻译
由于使用你正在使用 Gradle版本4.6或以上，当前版本的Android的Gradle插件不支持按需配置。
建议：通过在你的`gradle.properties` 文件中设置 `org.gradle.configureondemand=false` 禁用按需配置，或者使用一个低于4.6版本的Gradle。

##### 3.解决方法一：降级
打开 `gradle-wrapper.properties` 文件，修改distributionUrl 参数，将其后面修改为低于4.6版本的 Gradle。如：
```js
distributionUrl=https\://services.gradle.org/distributions/gradle-4.5-all.zip
```
##### 4.解决方法二：禁用按需配置
###### 1、打开 `gradle.properties` 文件，共有两个：`Global Properties` 和 `Project Properties`，将其中的

```org.gradle.configureondemand=true```

修改为

```org.gradle.configureondemand=false```

或删除该语句，或注释掉该语句。

###### 2、通过 File 菜单，点击 Settings打开设置。
###### 3、找到 Build, Execution, Deployment 里面的 Compiler，将右面的 Configure on demand 取消勾选。
如下所示：
![去除](https://github.com/suwu150/static-resource/blob/master/images/android/android%20stutio.png?raw=true)
        
        
【注意事项】
方法2中，第一步的 `gradle.properties` 文件，共有两个：Global Properties 和 Project Properties，全都需要修改。