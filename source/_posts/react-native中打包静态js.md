---
title: rēact-native中打包静态js
date: 2018-06-20 23:21:27
tags: react-native
---
在打包apk文件之前，我们需要将js文件代码和代码中使用的图片进行打包，然后提供给apk进行使用，打包命令行如下所示：
```js
react-native bundle --entry-file index.js --bundle-output ./android/app/src/main/assets/index.android.bundle --platform android --assets-dest ./android/app/src/main/res/ --dev true

```