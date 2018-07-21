---
title: Android中gradlew命令的基本使用
date: 2018-07-10 16:18:01
tags: Android
---

Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化建构工具。它使用一种基于Groovy的特定领域语言(DSL)来声明项目设置，抛弃了基于XML
的各种繁琐配置。 面向Java应用为主。当前其支持的语言限于Java、Groovy和Scala，计划未来将支持更多的语言。

build.gradle是Gradle默认的构建脚本文件，执行Gradle命令的时候，会默认加载当前目录下的build.gradle脚本文件，当然你也可以通过 -b 参数指定
想要加载执行的文件
下面进行对android项目进行分析。

```
├── app #Android App目录
│   ├── app.iml
│   ├── build #构建输出目录
│   ├── build.gradle #构建脚本
│   ├── libs #so相关库
│   ├── proguard-rules.pro #proguard混淆配置
│   └── src #源代码，资源等
├── module #Android 另外一个module目录
│   ├── module.iml
│   ├── build #构建输出目录
│   ├── build.gradle #构建脚本
│   ├── libs #so相关库
│   ├── proguard-rules.pro #proguard混淆配置
│   └── src #源代码，资源等
├── build
│   └── intermediates
├── build.gradle #工程构建文件
├── gradle
│   └── wrapper
├── gradle.properties #gradle的配置
├── gradlew #gradle wrapper linux shell脚本
├── gradlew.bat
├── LibSqlite.iml
├── local.properties #配置Androod SDK位置文件
└── settings.gradle #工程配置

```
gradlew 命令就是 gradle wrapper，是gradle的一个包装，可以理解为在这个项目本地就封装了gradle，
比如我的安卓项目是LoveYouDeeply, 在LoveYouDeeply/gradle/wrapper/gralde-wrapper.properties文件中声明了它指向的目录和版本，比如我的内容是：

```js
#Sun May 13 12:31:41 CST 2018
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip

```
如果你项目没有该文件的话，那么gradlew会到 ~/.gradle/wrapper/dists/gradle-xxx 下寻找，或者你新建给文件，内容复制到里面。其实你会发现是同一个目录咯。里面会有个随机数的文件夹，里面就是gradle，只要下载成功即可用grdlew wrapper的命令代替全局的gradle命令。

常用命令如下：(linux下是./gradlew，该脚本在项目下，windows直接gradlew即可)

+ ./gradlew -v 版本号，首次运行，没有gradle的要下载的哦。
```js
$ ./gradlew -v

------------------------------------------------------------
Gradle 4.6
------------------------------------------------------------

Build time:   2018-02-28 13:36:36 UTC
Revision:     8fa6ce7945b640e6168488e4417f9bb96e4ab46c

Groovy:       2.4.12
Ant:          Apache Ant(TM) version 1.9.9 compiled on February 2 2017
JVM:          1.8.0_171 (Oracle Corporation 25.171-b11)

```
+ ./gradlew clean 删除项目LoveYouDeeply/app目录下的build文件夹

+ ./gradlew build 检查依赖并编译打包

  - 这里注意的是 ./gradlew build 命令把debug、release环境的包都打出来，生成的包在目录LoveYouDeeply/app/build/outputs/apk/下。如果正式发布只需要打release的包，该怎么办呢，下面介绍一个很有用的命令 assemble, 如

+ ./gradlew assembleDebug 编译并打Debug包

+ ./gradlew assemblexiaomiDebug 编译并打xiaomi的debug包，其他类似

+ ./gradlew assembleRelease 编译并打Release的包

+ ./gradlew assemblexiaomiRelease 编译并打xiaomi的Release包，其他类似

+ ./gradlew installRelease Release模式打包并安装

+ ./gradlew uninstallRelease 卸载Release模式包

#### 补充

###### 1 gradlew build 和 gradle build 有区别吗?

使用gradle wrapper是gradle官方推荐的build方式，而gradlew正是运行了wrapper task之后生成的（运行wrapper task是Android Studio自动做的）。使用gralde wrapper的一个好处就是每个项目可以依赖不同版本的gradle，构建的时候gradle wrapper会帮你自动下载所依赖的版本的gradle。而如果你使用gradle build的话，同时你又有多个项目使用不同版本的gradle，那就需要你手动在自己的机器上配置多个版本的gradle，稍微麻烦一些

###### 2 自定义apk包名

gradle脚本可以使用下面方法进行命名：

```js
def releaseTime() {
    return new Date().format("yyyy-MM-dd", TimeZone.getTimeZone("UTC"))
}

//自定义apk安装包名
    // applicationVariants are e.g. debug, release
    applicationVariants.all { variant ->
        variant.outputs.each { output ->
            // For each separate APK per architecture, set a unique version code as described here:
            // http://tools.android.com/tech-docs/new-build-system/user-guide/apk-splits
            // def versionCodes = ["armeabi-v7a":1, "x86":2]
            // def abi = output.getFilter(OutputFile.ABI)
            // if (abi != null) {  // null for the universal-debug, universal-release variants
            //     output.versionCodeOverride =
            //             versionCodes.get(abi) * 1048576 + defaultConfig.versionCode
            // }
            def outputFile = output.outputFile
            def fileName
            if (outputFile != null && outputFile.name.endsWith('.apk')) {
                if (variant.buildType.name.equals('release')) {
                    fileName = "com.amarsoft.LoveYouDeeply_v${defaultConfig.versionName}.apk"
                } else if (variant.buildType.name.equals('debug')) {
                    fileName = "LoveYouDeeply_v${defaultConfig.versionName}_debug_${releaseTime()}.apk"
                }
                output.outputFile = new File(outputFile.parent, fileName)
            }
        }
    }


```
注意： 在Gradle 4.6中，应该采用下面的写法,而不是直接修改output
```js
    // applicationVariants are e.g. debug, release
    android.applicationVariants.all { variant ->
        variant.outputs.all { output ->
            // For each separate APK per architecture, set a unique version code as described here:
            // http://tools.android.com/tech-docs/new-build-system/user-guide/apk-splits
            // def versionCodes = ["armeabi-v7a":1, "x86":2]
            // def abi = output.getFilter(OutputFile.ABI)
            // if (abi != null) {  // null for the universal-debug, universal-release variants
            //     output.versionCodeOverride =
            //             versionCodes.get(abi) * 1048576 + defaultConfig.versionCode
            // }
            def fileName
            if (outputFile != null && outputFile.name.endsWith('.apk')) {
                if (variant.buildType.name.equals('release')) {
                    fileName = "com.amarsoft.LoveYouDeeply_v${defaultConfig.versionName}.apk"
                } else if (variant.buildType.name.equals('debug')) {
                    fileName = "LoveYouDeeply_v${defaultConfig.versionName}_debug_${releaseTime()}.apk"
                }
                outputFileName = fileName;
            }
        }
    }
```