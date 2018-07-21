---
title: react-native使用jenkins自动化构建android和ios应用实践
date: 2018-07-16 15:00:08
tags: react-native,jenkins
---

背景  

随着业务需求的演进，工程的复杂度会逐渐增加，自动化的践行日益强烈。事实上，工程的自动化一直是我们努力的目标，能有效提高我们的生产效率，最大化减少人为出错的概率，实现一些复杂的业务需求应变。
场景如下，公司现在的测试人员每次需要测试新版本，都需要开发人员打包，放到ftp，测试人员然后从ftp上拷贝到本地（或者用手机的ES文件管理器），再安装。尤其临近发版的一周，几乎每天都要新版本。这样的话，有两方面的影响：第一，打断了开发人员的开发进度；第二，开发人员打包效率低下，尤其是ios，不顺的话，总是打的不对（可能是证书的问题）。
要解决这个问题，必须实现移动端应用的自动化构建。具体说来就是，使用持续集成（CI）系统jenkins，自动检测并拉取最新代码，自动打包android的apk和ios的ipa，自动上传到内测分发平台蒲公英上。（接下来，测试人员只要打开一个（或多个）固定的网址，扫描一下二维码，就能下载最新的版本了…）

环境
因为要编译ios，所以选择Mac OSX 10.11.1。
无论是哪个操作系统，jenkins的配置是一样的。

```shell
 OS: macOS High Sierra 10.13.4
  Node: 8.3.0
  Yarn: Not Found
  npm: 5.3.0
  Watchman: 4.9.0
  Xcode: Xcode 9.3.1 Build version 9E501
  Android Studio: 3.1 AI-173.4819257

Packages: (wanted => installed)
  react: ^16.0.0 => 16.4.1
  react-native: ^0.51.0 => 0.51.1

```
安装Jenkins
官网地址：[http://jenkins-ci.org/](http://jenkins-ci.org/)
官网安装说明地址：[macOs系统安装](https://jenkins.io/doc/book/installing/#macos)
```shell
// 使用brew安装
brew install jenkins
// 启动，直接运行jenkins即可启动服务
jenkins
```
注意在启动过程中，会在终端中打印安全密码，先拷贝然后再打开下面默认的网址，进行继续安装，期间会要求输入密码，粘贴即可，就会出现如下界面

默认访问http://localhost:8080/, 可进入jenkins配置页面。