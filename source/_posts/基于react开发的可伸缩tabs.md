---
title: 基于react开发的可伸缩tabs
date: 2018-06-17 16:57:33
tags: react
---
1.效果如下所示：
![react-flex-tabs](https://github.com/suwu150/static-resource/blob/master/images/react/react-flex-tabs.gif?raw=true)
2.自己安装查看效果
  先克隆该项目
  ```
  git clone https://github.com/suwu150/react-flex-tabs.git
  ```
  然后通过以下命令安装依赖
  ```
  npm install
  ```
  通过以下命令运行项目
  ```
  npm start
  ```
3.flex-tabs组件简介
    能够通过传入的对象数据进行增加tab，每当点击传入数据时就会进行添加数据到总的tabs中，能够进行拖动交换位置、删除，当
    点击的tab超过页面能够展示的最大宽度时，进行将第一个tab进行折叠，也能够在折叠的tab中进行点击，展开需要打开的tab页面
    ，在折叠tab中也能够进行删除之前打开的tab 
4.使用说明
通过下面代码进行新增tab页面
```$js
    if (this.flexTabs) {
      const tab = {
        __id,
        name: data[__id]
      };
      this.flexTabs.createTab(tab);
    }
``` 
其中主要方法是createTab，进行创建tab，__id是tab的唯一编号，name用于展示在tab标签上面  
 