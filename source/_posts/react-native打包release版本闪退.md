---
title: react-native打包release版本闪退
date: 2018-07-17 12:09:52
tags: react-native
---
### undefined is not an object (evaluating 's.View.propTypes.style')

1.问题描述
我在用react-native进行项目打包的时候，我的debug模式在真机中能够运行，在release模式下运行时闪退，我在Android studio中查看logcat的时候，出现下面代码，
```shell
07-10 19:05:07.446 12390-12436/? E/ReactNativeJS: undefined is not an object (evaluating 's.View.propTypes.style')
07-10 19:05:07.455 12390-12436/? E/ReactNativeJS: Application Lova has not been registered.

    Hint: This error often happens when you're running the packager (local dev server) from a wrong folder. For example you have multiple apps and the packager is still running for the app you were working on before.
    If this is the case, simply kill the old packager instance (e.g. close the packager terminal window) and start the packager in the correct app folder (e.g. cd into app folder and run 'npm start').

    This error can also happen due to a require() error during initialization or failure to call AppRegistry.registerComponent.
07-10 19:05:08.012 1003-1271/? E/WifiStateMachine:  ConnectedState (when=-5ms what=131155 arg1=5!CMD_RSSI_POLL 5 0 "11223344" ac:c1:ee:3c:82:dd rssi=-51 f=2412 sc=60 link=65 tx=0.5, 0.0, 0.0  rx=0.3 bcn=0 [on:0 tx:0 rx:0 period:2983] from screen [on:0 period:-2082616660] gl hn u24 rssi=-46 ag=0 hr ticks 0,0,0 ls-=0 [56,56,56,56,61] brc=0 lrc=0
07-10 19:05:08.015 1003-1271/? E/WifiStateMachine:  L2ConnectedState (when=-8ms what=131155 arg1=5!CMD_RSSI_POLL 5 0 "11223344" ac:c1:ee:3c:82:dd rssi=-51 f=2412 sc=60 link=65 tx=0.5, 0.0, 0.0  rx=0.3 bcn=0 [on:0 tx:0 rx:0 period:3] from screen [on:0 period:-2082616657] gl hn u24 rssi=-46 ag=0 hr ticks 0,0,0 ls-=0 [56,56,56,56,61] brc=0 lrc=0
     get link layer stats 0
```
我的注册文件从如下所示：

```javascript
import { AppRegistry } from 'react-native';
import App from './App';

AppRegistry.registerComponent('Lova', () => App);

```
同时，我也在csdn进行了提问--[https://ask.csdn.net/questions/694368](https://ask.csdn.net/questions/694368)	

2.导致原因
错误不在于`Application Lova has not been registered`,其主要错误在于下面这段问题
```
E/ReactNativeJS: undefined is not an object (evaluating 's.View.propTypes.style')
```
对应到真实的react-native代码中，是如下代码问题：
```javascript
  static propTypes = {
    frontStyle: TextInput.propTypes.style,
    delimiterStyle: TextInput.propTypes.style,
    behindStyle: TextInput.propTypes.style,
    style: View.propTypes.style,
  };
```
是由于react-native库的问题，在使用debug的时候，`TextInput.propTypes.style`和`View.propTypes.style`是能够正常提供的，但是在release版本中，不能够正常提供
从而导致打包bundle.js的时候出错，这也就是当我们使用命令进行打包bundle.js的时候，采用--dev true的模式能够正常运行的原因

3.解决方案

在发布版本的时候删掉代码中的propsType校验`TextInput.propTypes.style`和`View.propTypes.style`或者直接就不用

注意：我当前使用的版本是
Environment:
  Node: 8.3.0
  npm: 5.3.0
  Watchman: 4.9.0
  Android Studio: 3.1 AI-173.4819257

Packages: (wanted => installed)
  react: 16.0.0 => 16.0.0
  react-native: ^0.51.0 => 0.51.1
