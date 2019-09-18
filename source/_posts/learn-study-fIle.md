1.Text组件中使用justifyContent不起作用，调整对齐请使用TextAlign  
2.默认主轴是column   
3.flex-wrap的使用，react-native默认为列显示   
  在列显示时,只有当设置了高度的时候，wrap和nowrap才能够起作用
     
    ```javascript
    {
            height: 80px;
            flex-flow: column nowrap;
    }
    ```
    
4.容器组件和内容组件使用时请注意   
5.能写在一行的代码不要写在两行    
6.时常注意当值为 0 的时候怎么处理 
7.android中不支持在Text中使用View标签，使用是会报下面错误 
  
```angularjs

invariant violation: Nesting of <View> within <Text> is not supported on Android

```

[](../images/react-native/onAndroid_no_in_view.png)