---
title: react-native打包发布apk
date: 2018-07-22 11:00:06
tags: react-native
---
Android要求所有应用都有一个数字签名才会被允许安装在用户手机上，所以在把应用发布到类似Google Play store这样的应用市场之前，你需要生成一个签名的APK包 

####1.生成一个签名密钥
你可以用keytool命令生成一个私有密钥。在Windows上keytool命令放在JDK的bin目录中（比如C:\Program Files\Java\jdkx.x.x_x\bin），你可能需要在命令行中先进入那个目录才能执行此命令。
值得注意的是，
keytool工具是java工具，[keytool工具详情查看](https://docs.oracle.com/javase/10/tools/keytool.htm#JSWOR-GUID-5990A2E4-78E3-47B7-AE75-6D1826259549)
也可以参照如下文章：[Keytool命令详解 ](https://blog.csdn.net/zlfing/article/details/77648430)
  在这里，主要对使用的命令进行说明，如下所示：
```
-genkey      在用户主目录中创建一个默认文件".keystore",还会产生一个mykey的别名，mykey中包含用户的公钥、私钥和证书
(在没有指定生成位置的情况下,keystore会存在用户系统默认目录，如：对于window xp系统，会生成在系统的C:/Documents and Settings/UserName/文件名为“.keystore”)
-v           显示密钥库中的证书详细信息
-keystore    指定密钥库的名称(产生的各类信息将不在.keystore文件中)
-alias       产生别名,每个keystore都关联一个独一无二的alias，这个alias通常不区分大小写
-keyalg      指定密钥的算法 (如 RSA  DSA（如果不指定默认采用DSA）)
-keysize     指定密钥长度
-validity    指定创建的证书有效期多少天
```
 学习了keytool的具体使用，我们能够开始运行下面命令进行生成签名，其中my-release-key.keystore就是要生成的签名，my-key-alias是生成签名的别名
```
$ keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```
在运行这条命令后，会要求你输入密钥库（keystore）和对应密钥的密码，然后设置一些发行相关的信息。最后它会生成一个叫做my-release-key.keystore（这个名称就是你在上面命令行中设置的，可以根据具体的需要进行更改）的密钥库文件。
在运行上面这条语句之后，密钥库里应该已经生成了一个单独的密钥，有效期为10000天。--alias参数后面的别名是你将来为应用签名时所需要用到的，所以记得记录这个别名。
注意：请记得妥善地保管好你的密钥库文件，不要上传到版本库或者其它的地方。

####2.设置gradle变量
把my-release-key.keystore文件放到你工程中的android/app文件夹下。
编辑~/.gradle/gradle.properties（没有这个文件你就创建一个）设置全局密钥设置或者在路径android/gradle.properties中添加如下的代码（注意把其中的****替换为相应密码,）
注意：~表示用户目录，比如windows上可能是C:\Users\用户名，而mac上可能是/Users/用户名。
```
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_STORE_PASSWORD=*****
MYAPP_RELEASE_KEY_PASSWORD=*****
```
上面的这些会作为全局的gradle变量，我们在后面的步骤中可以用来给应用签名。

####3.关于密钥库的注意事项:

一旦你在Play Store发布了你的应用，如果想修改签名，就必须用一个不同的包名来重新发布你的应用（这样也会丢失所有的下载数和评分）。所以请务必备份好你的密钥库和密码。

提示：如果你不想以明文方式保存密码，同时你使用的是macOS系统，那么你也可以把密码保存到钥匙串（Keychain）中。这样一来你就可以省略掉上面配置中的后两行（即MYAPP_RELEASE_STORE_PASSWORD和MYAPP_RELEASE_KEY_PASSWORD）。

####4.添加签名到项目的gradle配置文件
编辑你项目目录下的android/app/build.gradle，添加如下的签名配置：

```

...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            storeFile file(MYAPP_RELEASE_STORE_FILE)
            storePassword MYAPP_RELEASE_STORE_PASSWORD
            keyAlias MYAPP_RELEASE_KEY_ALIAS
            keyPassword MYAPP_RELEASE_KEY_PASSWORD
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...

```
#### 5.自定义生成apk包的名称
在生产中，我们经常会需要通过包名来进行区分打包版本的时间，那么我们如何自定义包名呢，我们只需要在android/app/build.gradle文件中添加如下代码即可：
首先需要在顶部定义获取时间的函数，如下：
```
// 定义获取时间的函数
def releaseTime() {
    return new Date().format("yyyy-MM-dd", TimeZone.getTimeZone("UTC"))
}
```
之后，在android对象中添加android.applicationVariants.all 函数方法定义，添加之后结果如下面所示：
```
...
    buildTypes {
        release {
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
            signingConfig signingConfigs.release
        }
    }
    // applicationVariants are e.g. debug, release
    android.applicationVariants.all { variant ->
        variant.outputs.all {
            def fileName
            if (variant.buildType.name.equals('release')) {
                fileName = "com.jkwu.LOVE_YOU_DEEPLY_v${defaultConfig.versionName}_${releaseTime()}.apk"
            } else if (variant.buildType.name.equals('debug')) {
                fileName = "LOVE_YOU_DEEPLY_v${defaultConfig.versionName}_debug_${releaseTime()}.apk"
            }
            outputFileName = fileName;
        }
    }
...
```
从上面代码中，我们能够看到自定义包名其实就是修改系统中之前的文件命名格式，修改成符合自己要求的格式即可

####6.生成发行APK包
只需在终端中运行以下命令：
```
cd android && ./gradlew assembleRelease
```
译注：cd android表示进入android目录（如果你已经在android目录中了那就不用输入了）。./gradlew assembleRelease在macOS、Linux或是windows的PowerShell环境中表示执行当前目录下的名为gradlew的脚本文件，且其运行参数为assembleRelease，注意这个./不可省略；而在windows的传统CMD命令行下则需要去掉./。

Gradle的assembleRelease参数会把所有用到的JavaScript代码都打包到一起，然后内置到APK包中。如果你想调整下这个行为（比如js代码以及静态资源打包的默认文件名或是目录结构等），可以看看android/app/build.gradle文件，然后修改以满足你的需求。
生成的APK文件位于android/app/build/outputs/apk/app-release.apk，它已经可以用来发布了。

测试应用的发行版本
在把发行版本提交到Play Store之前，你应该做一次最终测试。首先你需要用数据线连接你的开发机，在android目录下输入以下命令可以在设备上安装发行版本：
```
cd android && ./gradlew installRelease
```
注意installRelease参数只能在你完成了上面的签名配置之后才可以使用。 你现在可以关掉运行中的packager了，因为你所有的代码和框架依赖已经都被打包到apk包中，可以离线运行了。

在debug和release版本间来回切换安装时可能会报错签名不匹配，此时需要先卸载前一个版本再尝试安装。

#### 7. 缩小APK的大小
#####  (1).分割APK用ABI降低文件大小
  对于默认的配置，生成的APK为x86和armv7a CPU架构的原生代码。这使得它更容易分享在几乎所有的Android设备上运行。然而这有一个缺点，会有一些在任何设备上没有使用的原生代码，导致增大应用程序APK的大小。
你可以通过改变android/app/build.gradle文件，为每个CPU创建对应的APK，下面是修改的具体内容：
```
- def enableSeparateBuildPerCPUArchitecture = false
+ def enableSeparateBuildPerCPUArchitecture = true
```
上传这些文件到支持的应用市场，例如Google Play和亚马逊Appstore的用户将自动获得相应的APK。如果你想上传到如APKFiles其他应用市场，不支持多个应用程序的一个单一的应用程序，把下面的线以及创建的二进制文件的默认通用APK为CPU。

```
- universalApk false  // If true, also generate a universal APK
+ universalApk true  // If true, also generate a universal APK
```
打包之后结果如下所示
![universalApk true打包](https://img-blog.csdn.net/20180412173248113?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
 [Android 中的 armeabi,armeabi-v7a,x86,mips区别] (https://blog.csdn.net/guozikai/article/details/63255076)
#####  (2).启用Proguard代码混淆来缩小APK文件的大小（可选）
Proguard是一个Java字节码混淆压缩工具，它可以移除掉React Native Java（和它的依赖库中）中没有被使用到的部分，最终有效的减少APK的大小。

重要：启用Proguard之后，你必须再次全面地测试你的应用。Proguard有时候需要为你引入的每个原生库做一些额外的配置。参见app/proguard-rules.pro文件。

要启用Proguard，在android/app/build.gradle文件中设置minifyEnabled选项为true：
```
/**
 * 在release发行版中启用Proguard来减小 to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
```
启动混淆后需清空缓存 Android Studio Build > Clean Project, 否则可能会报错。

####8.[官方文档地址](http://facebook.github.io/react-native/docs/signed-apk-android.html)