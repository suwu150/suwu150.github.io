---
title: 基于react-native实现情侣小游戏
date: 2018-06-25 11:19:33
tags: react-native
--- 

--
#### 一、背景    

前段时间，突发奇想，想要自己做一款能够上架的app，就根据react-native进行了开发，其中具有的功能点就是情侣了解度的测试，通过一些情侣应该知道的问题进行测试双方的了解程度，了解的越多，则得分也就越高，在这个app里边区分男方和女方，用户点击进如对应的题库进行答
题，最终获取分值.   

#### 二、效果
![image](https://github.com/suwu150/static-resource/blob/master/images/react-native/react-native-love-you-deeply_2.gif?raw=true)  

#### 三、下载安装与查看
1.首先，通过命令进行克隆项目
```shell
git clone https://github.com/suwu150/LoveYouDeeply.git
```
也可以直接到网站[https://github.com/suwu150/LoveYouDeeply](https://github.com/suwu150/LoveYouDeeply)进行直接下载到本地
2.在下载之后，进入到项目中，执行下面命令进行安装依赖
```shell
npm install
```
确保安装没有出错，然后运行下面命令
`react-native run-ios`或者`react-native run-android`
注意，在mac系统中可直接运行`react-native run-ios`命令，windows系统中，运行react-native run-android这个之前需要确定有模拟器是启动着的或者有真机是连接着的。
按照上面的过程实施之后，就可以看到效果了.   
#### 四、代码及实现过程
1.项目结构如下所示： 

<center>
<img src="https://user-images.githubusercontent.com/20339760/41853485-73dbee9e-78c0-11e8-8c0c-e11b21f77207.png" width="35%" height="25%" />
项目结构
</center>
其中，具体代码写在src中，在src中`assets`中放置静态资源，components中放置使用到的组件，constants中放置静态常量，pages中放置具体的页面,utils中放置工具类 

1.主页面代码(home)
```js
import React, { Component } from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
import StringDistinction from 'react-native-string-distinction';
import styleDict from '../../constants/styleDict';

export default class Home extends Component {
  _toContent = (gender) => {
    const { navigation } = this.props;
    navigation.navigate('Content', { gender });
  };

  render() {
    const itemWidth = styleDict.windowW / 2;
    const itemHeight = styleDict.windowH - 10;
    return (
      <View style={{ flex: 1 }}>
        <View style={{ height: 100, justifyContent: 'center', alignItems: 'center' }}>
          <Text style={{ fontSize: 30, color: '#ffbbef' }}>选择主人公</Text>
        </View>
        <View style={{ flexDirection: 'row', justifyContent: 'center',
          alignItems: 'center', flex: 1 }}
        >
          <TouchableOpacity onPress={() => this._toContent('male')}>
            <View style={{ flex: 1, width: itemWidth, height: itemHeight, backgroundColor: '#fcff81',
              justifyContent: 'center', alignItems: 'center'
            }}
            >
              <StringDistinction
                value={'我是男主'}
                delimiter={'男'}
                frontStyle={{ fontSize: 20, color: '#fd7251' }}
                delimiterStyle={{ fontSize: 30, color: '#92cbfd' }}
                behindStyle={{ fontSize: 26, color: '#fd7251' }}
              />
            </View>
          </TouchableOpacity>
          <TouchableOpacity onPress={() => this._toContent('female')}>
            <View style={{ flex: 1, width: itemWidth, height: itemHeight, backgroundColor: '#ffbbef',
              justifyContent: 'center', alignItems: 'center'
            }}
            >
              <StringDistinction
                value={'我是女主'}
                delimiter={'女'}
                frontStyle={{ fontSize: 20, color: '#fd7251' }}
                delimiterStyle={{ fontSize: 30, color: '#fd697d' }}
                behindStyle={{ fontSize: 26, color: '#fd7251' }}
              />
            </View>
          </TouchableOpacity>
        </View>
        <Text>选择进入测试...</Text>
      </View>
    );
  }
}

```
2.内容界面(content)代码如下所示：
```js
import React, { Component } from 'react';
import { View, Text, Image, StyleSheet } from 'react-native';
import SwipeCards from 'react-native-swipe-cards';
import Card from './card';
import NoMoreCard from './noMoreCard';
import styleDict from '../../constants/styleDict';
import randomStyle from '../../utils/randomStyle';

import femaleQuestions from '../../constants/female_questions.json';
import maleQuestions from '../../constants/male_questions.json';

export default class Home extends Component {
  constructor(props) {
    super(props);
    const { navigation } = this.props;
    const { params } = navigation.state;
    this.state = {
      gender: params.gender,
      questions: (params.gender === 'male' ? maleQuestions : femaleQuestions) || [],
      totalScore: 0
    };
  }

  _handleYup = (card) => {
    // 右划-否
    console.log(`Yup for ${card.text}`);
    this.setState({
      totalScore: this.state.totalScore - card.score <= 0 ? 0 : this.state.totalScore - card.score
    });
  };

  _handleNope = (card) => {
    // 左划-是
    console.log(`Nope for ${card.text}`);
    this.setState({
      totalScore: this.state.totalScore + card.score
    });
  };

  _handleMaybe = (card) => {
    // 上划
    console.log(`Maybe for ${card.text}`);
    this.setState({
      totalScore: this.state.totalScore + (card.score / 2)
    });
  };

  render() {
    const explainItem = styleDict.windowW / 3;
    return (
      <View style={{ flex: 1, alignItems: 'center' }}>
        <View style={{ flexDirection: 'row', alignItems: 'center', width: styleDict.windowW,
          height: 30, position: 'absolute', top: 0, zIndex: 999 }}
        >
          <View
            style={{ width: explainItem, backgroundColor: '#ffa6f3', height: 30, borderBottomRightRadius: 30,
              alignItems: 'center', justifyContent: 'center'
            }}
          >
            <Text
              style={{ alignSelf: 'flex-start', textAlign: 'left' }}
            >
              {'左滑选是'}
            </Text>
          </View>
          <View
            style={{ width: explainItem, backgroundColor: '#ffa6f3', height: 30, borderRadius: 30,
              alignItems: 'center', justifyContent: 'center' }}
          >
            <Text
              style={{ alignSelf: 'center', width: explainItem, textAlign: 'center' }}
            >
              {'上滑也许是吧'}
            </Text>
          </View>
          <View
            style={{ width: explainItem, backgroundColor: '#ffa6f3', height: 30, borderBottomLeftRadius: 30,
              alignItems: 'center', justifyContent: 'center' }}
          >
            <Text
              style={{ alignSelf: 'flex-end', width: explainItem, textAlign: 'right' }}
            >
              {'右滑选否'}
            </Text>
          </View>
        </View>
        <View style={{ flex: 1 }}>
          <SwipeCards
            cards={this.state.questions}
            renderCard={(cardData) => <Card {...cardData} />}
            renderNoMoreCards={() => <NoMoreCard />}
            handleYup={this._handleYup}
            handleNope={this._handleNope}
            handleMaybe={this._handleMaybe}
            yupTextStyle={{ color: '#ff6559' }}
            yupText="否"
            yupStyle={{ borderColor: '#ff6559' }}
            nopeStyle={{ borderColor: '#3d7d29' }}
            nopeTextStyle={{ color: '#3d7d29' }}
            nopeText="是"
            maybeText="也许是吧"
            hasMaybeAction
          />
        </View>
        <Image
          source={require('../../assets/images/heart.jpg')}
          style={{ width: styleDict.windowW, height: 50, resizeMode: Image.resizeMode.stretch, opacity: 0.7 }}
        />
        <View style={{
          height: 150,
          width: styleDict.windowW,
          justifyContent: 'center',
          alignItems: 'center',
          backgroundColor: this.state.gender === 'male' ? '#ffe144' : '#ffa0c4'
        }}
        >
          <Text style={randomStyle()}>{this.state.totalScore}</Text>
        </View>
      </View>
    );
  }
}

```
#### 五、下一版本待做功能
[].增加远程服务器，进行数据存储   
[].增加用户自己提交问题的功能

#### 六、仓库地址
欢迎访问代码仓库：[https://github.com/suwu150/LoveYouDeeply](https://github.com/suwu150/LoveYouDeeply) 点赞加星星

#### 七、发布地址
安卓app已经发布到[百度手机助手](http://shouji.baidu.com/software/24185511.html),点击下载装到你的手机吧！！
其他发布地址正在跟进中...


    
    