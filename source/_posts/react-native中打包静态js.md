---
title: rēact-native中打包静态js
date: 2018-06-20 23:21:27
tags: react-native,android
---
```js
react-native bundle --entry-file index.js --bundle-output ./android/app/src/main/assets/index.android.bundle --platform android --assets-dest ./android/app/src/main/res/ --dev true

```