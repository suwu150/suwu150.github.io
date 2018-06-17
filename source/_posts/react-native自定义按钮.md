---
title: react-native自定义按钮
date: 2018-06-17 20:27:59
tags: react-native
---
The final rendering
----
![The final rendering](https://github.com/suwu150/static-resource/blob/master/images/react-native-custome-button.gif?raw=true)
![The final rendering-image](https://github.com/suwu150/static-resource/blob/master/images/react-native-custome-button-image.gif?raw=true)          

Description: 
--------------------------------------    
A custom button component that should render nicely on any platform. Supports a minimal level of customization.     

Installation:  
-------------------------------------- 
```
 npm install --save react-native-customer-button
```
Example usage: 
--------------------------------------- 
1.basic     

```
import Button from 'react-native-customer-button';
   ...
   _onPress = () => {
     console.log('onPress');
   }
  render() {
    return (
      <View style={styles.container}>
        <Button onPress={this._onPress} />
      </View>
    );
  }
```

Props:   
---------------------------------------

|Props|Explain|type|require|default|          
|:-------|:--------|:--------|:-------|:----------|
|onPress|handler to be called when the user taps the button|function|yes|no|       
|title|text to display inside the button|string|yes|ok|     
|disabled|If true, disable all interactions for this component.|bool|no|false|      
|style|button style,The style used to modify the button|object|no|{ backgroundColor: '#43CCFF',height: 30,width: 100,}|     
|textStyle|The style of the text in the button|object|no|{fontSize: 15, color: '#000000'}|  
|icon|button icon file resource, resource file introduced by require|Image resources|no|no|     
|iconStyle|Image's style|object|no|{}|     

Examples from props:
```javascript
 render() {
    return (
      <View style={styles.container}>
        <Text style={{ marginBottom: 10 }}>{this.state.count}</Text>
        <Button
          style={{ marginBottom: 5 }}
          onPress={() => this.setState({ count: this.state.count + 1 })}
        />
        <Button
          title={'cancel'}
          style={{ marginBottom: 5 }}
          onPress={() => this.setState({ count: 'cancel' })}
        />
        <Button
          title={'submit'}
          style={{
            marginBottom: 5,
            backgroundColor: 'gray'
          }}
          onPress={() => this.setState({ count: 'submit' })}
        />
        <Button
          title={'white font'}
          style={{
            marginBottom: 5,
            backgroundColor: 'gray'
          }}
          textStyle={{
            color: '#fff',
            fontSize: 16
          }}
          underlayColor={'#ff0'}
          onPress={() => this.setState({ count: 'submit' })}
        />
        <Button
          title={'white font'}
          style={{
            marginBottom: 5,
            backgroundColor: 'gray'
          }}
          textStyle={{
            color: '#fff',
            fontSize: 16
          }}
          icon={require('../constants/icon_user.png')}
          underlayColor={'#ff0'}
          onPress={() => this.setState({ count: 'submit' })}
        />
      </View>
    );
  }
```
Code
------------------------------------

```js
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import {
  ViewPropTypes,
  TouchableOpacity,
  TouchableHighlight,
  Text,
  View,
  StyleSheet,
  Image
} from 'react-native';

const NoDoublePress = {
  lastPressTime: 1,
  onPress(callback) {
    const curTime = new Date().getTime();
    if (curTime - this.lastPressTime > 1000) {
      this.lastPressTime = curTime;
      callback();
    }
  },
};

const styles = StyleSheet.create({
  defaultStyle: {
    backgroundColor: '#43CCFF',
    justifyContent: 'center',
    alignItems: 'center',
    height: 30,
    width: 100,
  },
  defaultTouchStyle: {
    width: '100%',
    height: '100%',
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center'
  },
  defaultTextStyle: {
    fontSize: 15,
    color: '#000000'
  },
  defaultRowContainer: {
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center'
  }
});

class RNButton extends Component {
  constructor(props) {
    super(props);
    this.state = {};
  }

  static propTypes = {
    title: PropTypes.string,
    style: ViewPropTypes.style,
    underlayColor: PropTypes.string,
  };

  static defaultProps = {
    title: 'ok',
  };

  onPress = (e) => {
    NoDoublePress.onPress(() => {
      this.props.onPress && this.props.onPress(e);
    });
  };

  renderIcon = () => {
    const { icon, iconStyle = {} } = this.props;
    return (
      <Image source={icon} style={iconStyle} />
    );
  };

  render() {
    const { title } = this.props;
    if (this.props.underlayColor) {
      return (
        <View style={[styles.defaultStyle, this.props.style]}>
          <TouchableHighlight
            style={[styles.defaultTouchStyle]}
            onPress={this.onPress}
            underlayColor={this.props.underlayColor}
          >
            <View style={styles.defaultRowContainer}>
              {this.renderIcon()}
              <Text
                style={[styles.defaultTextStyle, this.props.textStyle]}
              >
                {this.props.title}
              </Text>
            </View>
          </TouchableHighlight>
        </View>
      );
    }

    return (
      <View style={[styles.defaultStyle, this.props.style]}>
        <TouchableOpacity
          style={[styles.defaultTouchStyle]}
          onPress={this.onPress}
        >
          <View style={styles.defaultRowContainer}>
            {this.renderIcon()}
            <Text
              style={[styles.defaultTextStyle, this.props.textStyle]}
            >
              {this.props.title}
            </Text>
          </View>
        </TouchableOpacity>
      </View>

    );
  }
}

export default RNButton;

```