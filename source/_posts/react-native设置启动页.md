---
title: react-native设置应用启动页
date: 2018-07-10 16:09:52
tags: react-native
---
#### 一、背景
  在我们使用react-native进行编写代码的时候，当启动应用的时候，我们会看到如下界面
  <center>
<img src="https://img-blog.csdn.net/2018072911561772?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width='50%' height='50%' />
  </center>
  然而，这样的启动界面是非常的不又好，那么我们该怎么进行处理启动界面呢？有如下两种方案
#### 二、方案
+ 1.使用第三方库(react-native-splash-screen)
+ 2.ios系统设置（仅适用ios系统，在这里不做讲解）
#### 三、具体实现方式
##### 一）.react-native-splash-screen
###### 1.安装
```shell
npm i react-native-splash-screen --save
```
###### 2.链接到底层代码
   1.自动配置链接
```shell
    react-native link react-native-splash-screen
```
or

```
    rnpm link react-native-splash-screen
```
 2.手动配置链接 
 Android手动配置:
   
   （1）在`android/settings.gradle`文件中添加如下代码：
```javascript
include ':react-native-splash-screen'
project(':react-native-splash-screen').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-splash-screen/android')

```

   （2） 在`android/app/build.gradle`文件中,dependencies对象内添加如下代码：  
     
```
  dependencies {
      ...
      compile project(':react-native-splash-screen')
      ...
  }
  
```
      
   （3）在 `MainApplication.java`文件中添加如下代码：
      
```
    import org.devio.rn.splashscreen.SplashScreenReactPackage;
    ...
      @Override
      protected List<ReactPackage> getPackages() {
        return Arrays.<ReactPackage>asList(
            new MainReactPackage(),
            new SplashScreenReactPackage(), // 添加启动页代码
        );
      }
```
以上就是android系统中链接的方式，下面是ios底层链接的方式
ios手动配置：
   （1）在 XCode中, 点击项目，打开展开项目右键点击 `Libraries ➜ Add Files to [your project's name]`,选中路径 `node_modules ➜ react-native-splash-screen and add SplashScreen.xcodeproj`添加SplashScreen.xcodeproj项目文件
   
   （2）在 XCode中, 点击项目，打开展开项目, 选中你的项目. 添加 `libSplashScreen.a` 到你的项目的 `Build Phases ➜ Link Binary With Libraries`中，界面如下所示：
    
  ![Xcode添加Link Binary With Libraries](https://img-blog.csdn.net/20180729120516745?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
   
   （3）修复 `'SplashScreen.h' file not found`, 选择你的项目 → Build Settings → Search Paths → Header Search Paths to add:

```
$(SRCROOT)/../node_modules/react-native-splash-screen/ios
```

   界面如下所示：
![Build Settings](https://img-blog.csdn.net/2018072912061532?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
以上就是ios系统的配置链接的方法，下面就让我们来看看具体在代码中的使用

###### 3.进行使用
 + android： 
   （1）在`MainActivity.java`文件中添加如下代码：
 ```
     import android.os.Bundle; // here
     import com.facebook.react.ReactActivity;
     import org.devio.rn.splashscreen.SplashScreen; // 启动页设置添加代码
    
     public class MainActivity extends ReactActivity {
         /**
          * 设置启动页
          */
         @Override
         protected void onCreate(Bundle savedInstanceState) {
             SplashScreen.show(this);  // 展示启动页设置代码
             super.onCreate(savedInstanceState);
         }
         // ...other code
     }
 
 ```
   （2）添加启动页图片及布局
         在路径`app/src/main/res/layout`创建文件(如果不存在则进行手动创建)命名为 `launch_screen.xml`. 然后输入下面内容：
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
     android:orientation="vertical" android:layout_width="match_parent"
     android:layout_height="match_parent"
     android:background="@drawable/launch_screen">
    </LinearLayout>
```
   （3）在res文件夹下面创建如下文件夹，添加对应分辨率图片，分别对应放入下面文件夹,图片命名和xml中一致，命名为`launch_screen`

```
         drawable-ldpi
         drawable-mdpi
         drawable-hdpi
         drawable-xhdpi
         drawable-xxhdpi
         drawable-xxxhdpi
```

   到这里，我们已经能够在界面中能够看到启动页的加载了，但是为了能有更好的效果，我们继续往下看,在文件夹`app/src/main/res/values/colors.xml`中添加一个颜色命名为` status_bar_color` ,其中status_bar_color为状态栏颜色设置，代码如下所示：       
```javascript
         <?xml version="1.0" encoding="utf-8"?>
         <resources>
             <color name="status_bar_color">#FF0000</color>
         </resources>
```
   同时我们能够在启动的过程中看到有白屏出现，我们将启动背景设置成透明背景，使用下面方式进行处理， 打开`android/app/src/main/res/values/styles.xml`文件，并且添加 ` <item name="android:windowIsTranslucent">true</item>`
   到文件中，添加之后结果如下所示:    
```javascript
         <resources>
             <!-- Base application theme. -->
             <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
                 <!-- Customize your theme here. -->
                 <!--设置透明背景-->
                 <item name="android:windowIsTranslucent">true</item>
             </style>
         </resources>
```
当然，你也可以使用自定义的启动颜色，如下面所示：

在路径`android/app/src/main/res/values/colors.xml`文件添加如下代码用于设置状态栏
```javascript
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="status_bar_color"><!-- Colour of your status bar here --></color>
</resources>
```
在路径`android/app/src/main/res/values/styles.xml`中添加自定义样式，代码如下所示:
```javascript
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="SplashScreenTheme" parent="SplashScreen_SplashTheme">
        <item name="colorPrimaryDark">@color/status_bar_color</item>
    </style>
</resources>
```
然后修改启动页展示的样式选择就可以了:

```javascript
SplashScreen.show(this, R.style.SplashScreenTheme);
```
效果如下所示,我们能够看到顶部红色的状态栏：
<center>
<img src="https://img-blog.csdn.net/20180729120844403?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" />
[ios_lanch_screen_custom.gif]
</center>


 + ios：
 更新`AppDelegate.m`文件如下所示代码：

```c++
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
#import <React/RCTRootView.h>
#import <React/RCTRootView.h>
#import "RNSplashScreen.h"  // 导入启动页组件库

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  NSURL *jsCodeLocation;

  jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];

  RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                      moduleName:@"LoveYouDeeply"
                                               initialProperties:nil
                                                   launchOptions:launchOptions];
  rootView.backgroundColor = [[UIColor alloc] initWithRed:1.0f green:1.0f blue:1.0f alpha:1];

  self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
  UIViewController *rootViewController = [UIViewController new];
  rootViewController.view = rootView;
  self.window.rootViewController = rootViewController;
  [self.window makeKeyAndVisible];
  [RNSplashScreen show];  // 添加启动页展示
  return YES;
}

@end

```
然后通过LaunchImage or LaunchScreen.xib自定义启动页,其具体过程如下所示，
  1、用Xcode打开ios工程，找到Image.xcassets并点击选中，在空白处选择 App Icons & Launch Images ➜ New ios Launch Image , 完成这步后会生成一个LaunchImage，操作界面如下所示：
  ![new iOS launch image](https://img-blog.csdn.net/20180729121229540?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
  2、选中Image.xcassets ➜ LaunchImage，就是上一步创建的LaunchImage，右侧框中的部分是让你选择要支持的系统，横竖屏之类的（这个按照需求选择，如果你的项目不打算支持ios6可以不选择）。然后点击中间部分选中一个分辨率的框，上传相应分辨率的图片作为启动屏幕

以下是选择框中不同屏幕的分辨率,按照下面给出的像素进行制作特定大小的图片添加即可：

```javascript
iPhone Portrait iOS 8-Retina HD 5.5 （1242×2208） @3x
iPhone Portrait iOS 8-Retina HD 5.5 （1242×2208） @3x
iPhone Portrait iOS 8-Retina HD 4.7 （750×1334） @2x
iPhone Portrait iOS 7,8-2x （640×960） @2x
iPhone Portrait iOS 7,8-Retina 4 （640×1136） @2x
iPhone Portrait iOS 5,6-1x （320×480） @1x
iPhone Portrait iOS 5,6-2x （640×960） @2x
iPhone Portrait iOS 5,6-Retina4 （640×1136） @2x
```
3、选中LaunchScreen.xib,会有个弹出框，默认选择确定就行，然后把右边的 Use Launch Screen 取消选中（因为ios可以用来自定义图片启动屏幕或通过 LaunchScreen.xib启动屏幕，ios默认s设置，我们在这里取消掉默认设置的）。
界面如下所示：
![remove location](https://img-blog.csdn.net/20180729121340749?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
4、如图选中项目工程，右侧会出现工程的基本配置，设置Launch Images Srouce配置为LaunchImage（如果没有LaunchImage会弹出一个框提示拷贝图片，按照默认点确定就行），然后设置Launch Screen File为空（这个很重要）。

现在，我们所有的准备工作都已经完成，下面就是在js代码中的使用，在React-Native代码中进行隐藏启动页，
当我们准备好js代码之后，我们就可以将我们的启动页进行隐藏掉，其中隐藏启动页的代码如下所思：
```javascript
import React, { Component } from 'react';
import SplashScreen from 'react-native-splash-screen';
import Router from './src/routerManager';

export default class App extends Component {
  constructor(props) {
    super(props);
      // do anything while splash screen keeps, use await to wait for an async task.
      // 在入口文件处隐藏掉启动页
      SplashScreen.hide(); // 关闭启动屏幕
  }
  render() {
    return (
      <Router {...this.props} />
    );
  }
}
```

在ios系统配置中，
#### 四、效果展示
Android：
<center>
<img src="https://img-blog.csdn.net/20180729123218647?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1d3UxNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" />
</center>


-----
以上就是启动页的设置解决方案
