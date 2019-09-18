---
title: 前端开发者不得不知的ES6十大特性
date: 2018-07-28 23:39:29
tags: ES6
---
## 前端开发者不得不知的ES6十大特性
ES6是什么, 实际上, 它是一种新的javascript规范。在这个大家都很忙碌的时代，如果你想对ES6有一个快速的了解，那么请继续往下读，去了解当今最流行的编程语言JavaScript最新一代的十大特性。
以下是ES6排名前十的最佳特性列表（排名不分先后）：

1.Default Parameters（默认参数） in ES6
2.Template Literals （模板文本）in ES6
3.Multi-line Strings （多行字符串）in ES6
4.Destructuring Assignment （解构赋值）in ES6
5.Enhanced Object Literals （增强的对象文本）in ES6
6.Arrow Functions （箭头函数）in ES6
7.Promises in ES6
8.Block-Scoped Constructs Let and Const（块作用域构造Let and Const）
9.Classes（类） in ES6
10.Modules（模块） in ES6

声明：这里只列出了10条比较常用的特性。
首先回顾一下JavaScript的历史，不清楚历史的人，很难理解JavaScript为什么会这样发展。下面就是一个简单的JavaScript发展时间轴：

1、1995：JavaScript诞生，它的初始名叫LiveScript。
2、1997：ECMAScript标准确立。
3、1999：ES3出现，与此同时IE5风靡一时。
4、2000–2005： XMLHttpRequest又名AJAX， 在Outlook Web Access (2000)、Oddpost (2002)，Gmail (2004)和Google Maps (2005)大受重用。
5、2009： ES5出现，（就是我们大多数人现在使用的）例如foreach，Object.keys，Object.create和JSON标准。
6、2015：ES6/ECMAScript2015出现。

历史回顾就先到此，现让我们进入正题。

1.Default Parameters（默认参数） in ES6
还记得我们以前不得不通过下面方式来定义默认参数：
```
var link = function (height, color, url) {
    var height = height || 50;
    var color = color || 'red';
    var url = url || 'http://www.baidu.com';
    ...
}
```
上面这种方案解决了大多数问题，但是，当我们的值是`0`时就会出现问题，如下，请说出下面代码打印结果：
```
log = (data) => console.log('输出结果为：' + data);
var link = function (height, color, url) {
   var height = height || 50;
   var color = color || 'red';
   var url = url || 'http://www.baidu.com';
   log(height);
   log(color);
   log(url);
 };

 link();
 link(23,'blue', 'www.jkwu.club');
 link(0,0,0);
```
如上面代码结果，我们知道因为在JavaScript中，`0`表示布尔值false，它是默认被[hard-coded](http://blog.csdn.net/leonhughes/article/details/4712792)的值，而不能变成参数本身的值，但在ES6，我们可以直接把默认值放在函数声明里：
如下面代码：
```
var link = function (height = 50, color = 'red', url = 'http://www.baidu.com') {
   log(height);
   log(color);
   log(url);
 };

 link();
 link(23,'blue', 'www.jkwu.club');
 link(0,0,0);
```
在函数声明时进行默认值的设置，结果如下所示：
```
输出结果为：50
输出结果为：red
输出结果为：http://www.baidu.com
输出结果为：23
输出结果为：blue
输出结果为：www.jkwu.club
输出结果为：0
输出结果为：0
输出结果为：0
```
2.Template Literals （模板文本）in ES6
在其它语言中，使用模板和插入值是在字符串里面输出变量的一种方式。因此，在ES5，我们可以这样组合一个字符串：
```
var name = 'Your name is ' + first + ' ' + last + '.';
var url = 'http://localhost:3000/api/messages/' + id;
```
幸运的是，在ES6中，我们可以使用新的语法$ {NAME}，并把它放在反引号里：
```
var name = `Your name is ${first} ${last}. `;
var url = `http://localhost:3000/api/messages/${id}`;
```
3.Multi-line Strings （多行字符串）in ES6
ES6的多行字符串是一个非常实用的功能。在ES5中，我们不得不使用以下方法来表示多行字符串：
```
var roadPoem = 'Then took the other, as just as fair,nt'
    + 'And having perhaps the better claimnt'
    + 'Because it was grassy and wanted wear,nt'
    + 'Though as for that the passing therent'
    + 'Had worn them really about the same,nt';
var fourAgreements = 'You have the right to be you.n
    You can only be you when you do your best.';
```
然而在ES6中，仅仅用反引号就可以解决了：
```
var roadPoem = `Then took the other, as just as fair,
    And having perhaps the better claim
    Because it was grassy and wanted wear,
    Though as for that the passing there
    Had worn them really about the same,`;
var fourAgreements = `You have the right to be you.
    You can only be you when you do your best.`;
```
4.Destructuring Assignment （解构赋值）in ES6
解构可能是一个比较难以掌握的概念。先从一个简单的赋值讲起，其中house 和 mouse是key，同时house 和mouse也是一个变量，在ES5中是这样：
```
var data = $('body').data(), // data has properties house and mouse
   house = data.house,
   mouse = data.mouse;
```
以及在node.js中用ES5是这样：
```
var jsonMiddleware = require('body-parser').jsonMiddleware ;
var body = req.body, // body has username and password
   username = body.username,
   password = body.password;
```
在ES6，我们可以使用这些语句代替上面的ES5代码：
```
var { house, mouse} = $('body').data(); // we'll get house and mouse variables
var {jsonMiddleware} = require('body-parser');
var {username, password} = req.body;
```
这个同样也适用于数组，非常赞的用法：
```
var [col1, col2]  = $('.column'),
   [line1, line2, line3, line5] = file.split('n');
```
我们可能需要一些时间来习惯解构赋值语法的使用，但是它确实能给我们带来许多意外的收获。
5.Enhanced Object Literals （增强的对象文本）in ES6
相对于ES5而言,ES6的对象字面量得到了很大程度的增强。这些改进我们可以输入更少的代码同时语法更易于理解。那就一起来看看对象增强的功能。对象字面量简写(Object Literal Shorthand)、对象方法简写(Object Method Shorthand)、计算对象键(Object key)、对象解构(Object Destructuring)。
`ES6对象字面量简写`
javascript中的对象使用对象字面量很容易创建，现在来使用ES5来创建一个对象，在music中两个对象属性 type和heat，他们的分别也是来之于type和heat。代码如下：
ES5对象字面量
```
var type = 'rock';
var heat = '50%';
var music = {
  type: type,
  heat: heat
};
console.log(music);  // Object {type: "rock", heat: "50%"}
```
ES6对象字面量
然而我们现在可以使用ES6的对象重新写这个例子。在ES6中如何你的对象属性名和当然作用域中的变量名相同，那么现在必须要在把这个type或heat书写两次。ES6的对象会自动的帮你完成键到值的赋值。这样看起来代码更优雅也能节省一半的字符输入量。代码如下：
```
var type = 'rock';
var heat = '50%';
var music = {
  type,
  heat
};
console.log(music);  // Object {type: "rock", heat: "50%"}
```
ES5返回一个对象
假如我们创建一个函数他做了一些运算然后要返回这个函数中某些完成运算的变量为一个对象(函数返回多个值)，比如：type或heat。在ES5中我们是这样写的。
```
function getMusic() {
  var type = 'rock';
  var heat = '50%';
  // 一些运算
  return { type: type, heat: heat };
}
console.log(getMusic().type);   // rock
console.log(getMusic().heat);   // 50%
```
ES6返回一个对象
现在使用ES6简洁优雅的重写这个函数返回一个对象，这里还是使用上面的函数，只是在返回对象的时候使用ES6的语法
```
function getMusic() {
  var type = 'rock';
  var heat = '50%';
  // 一些运算
  return { type, heat };
}
console.log(getMusic().type); // rock
console.log(getMusic().heat); // 50%
```
ES6对象方法简写
对象不仅仅是用来保存数据，他还可以用来保存函数。在ES5中我们也是通过给定一个键然后再给定一个匿名函数或命名函数。代码如下：

ES5对象方法
```
var type = 'rock';
var heat = '50%';
var music = {
  type: type,
  heat: heat,
  description: function() {
    return '当前音乐风格为' + this.type + ',' + this.heat + '都喜欢';
  }
}
console.log(music.description()); // 当前音乐风格为rock,50%都喜欢
```
ES6对象方法
使用ES5语法重写上面的music对象，必须要写上对象键然后还要写上function关键字。而在es6中只需要方法名和圆括号再跟上花括号即可。代码如下：
```
var type = 'rock';
var heat = '50%';
var music = {
  type,
  heat,
  description() {
    return '当前音乐风格为' + this.type + ',' + this.heat + '都喜欢';
  }
}
console.log(music.description()); // 当前音乐风格为rock,50%都喜欢
```
ES6对象方法简写和字符串模板
还是使用上面的对象music，我们有一个方法description他返回的是一个字符串，但是字符串的拼接过程可以说是相当吃力的，如果稍微不注意很容易出错。使用ES6的字符串模将完美解决这个问题。字符串模板使用``将字符串包裹起来里面的变量使用${}包裹起来。代码如下：
```
var type = 'rock';
var heat = '50%';
var music = {
  type,
  heat,
  description() {
    return `当前音乐风格为${this.type},${this.heat}都喜欢'`;
  }
}
console.log(music.description()); // 当前音乐风格为rock,50%都喜欢'
```
ES6计算对象键(Keys)
在ES5中对象可以理解为一个关联数组或一个hashmaps。在ES5中创建对象的键就三种object.xx、object['xx']、Object.defineProperty可以用来构建对象的键。在ES6中可以使用更多的方法来创建。
ES6计算对象键
在这次的music对象中，我们要使用一个变量field作为我们对象的键，heat作为这个键的值。代码如下：
```
var heat = '50%';
var field = 'rock';
var music = {
  [field]: heat
}
console.log(music); // Object {rock: "50%"}
```
在ES5中也可以使用如下代码定义，但是~~额。
```
function _defineProperty(obj, key, value) {
  if (key in obj) {
    Object.defineProperty(obj, key, {value: value, enumerable: true, configurable: true, writable: true});
  } else {
    obj[key] = value;
  }
  return obj;
}
var heat = '50%';
var field = 'rock';
var music = _defineProperty({}, field, heat);
console.log(music)
```
ES6对象键计算表达式
可以在对象键的变量上调用方法 wow！！！
```
var heat = '50%';
var field = 'Rock and Roll';
var music = {
  [field.toLowerCase()]: heat
}
console.log(music); // Object {rock and roll: "50%"}
```
ES5同样也是可以实现，只是~~有点不好看
```
function _defineProperty(obj, key, value) {
  if (key in obj) {
    Object.defineProperty(obj, key, {value: value, enumerable: true, configurable: true, writable: true});
  } else {
    obj[key] = value;
  }
  return obj;
}
var heat = '50%';
var field = 'Rock and Roll';
var music = _defineProperty({}, field.toLowerCase(), heat);
console.log(music); // Object {rock and roll: "50%"}
```
还可以使用不同的数组方法为我们的对象键赋值，使用[]将会计算对象的键值。代码如下：
```
let people = [20, 25, 30];
let music = {
  people,
  [people]: 'They all love rock and roll',
  [people.length]: 'object key is 3',
  [{}]: 'empty object'
}
console.log(music);
console.log(music.people);
console.log(music['people']);
console.log(music[people]);
console.log(music[people.length]);
console.log(music['[object Object]']);
console.log(music[music]);
/*
Object {3: "object key is 3", people: Array[3], 20,25,30: "They all love rock and roll", [object Object]: "empty object"}
[20, 25, 30]
[20, 25, 30]
They all love rock and roll
object key is 3
empty object
empty object
*/
```
ES6对象解构
把对象或者数组中的数据取出使用是我们经常要使用的功能，ES6有新的功能让我们快捷拆分对象和数组中的值。在=号右边是要解构的对象或数组，=号左边是要接收创建的变量。代码如下：

对象解构1
=号左边的类型要和右边对应，比如右边是一个对象类型则左边也需要使用对象字面量包裹。右边是数组则左边也需要使用数组包裹。
```
let music = {
  type: 'rock',
  heat: '50%'
};
let { type, heat } = music;
console.log(type, heat);  // rock 50%
```
数组解构
```
let people = [20, 25, 30]
let [young, oldYoung] = people;
console.log(young, oldYoung) // 20 25
```
ES6对象解构重命名
有时候我们不想使用解构对象中的键来新创建变量，需要解构后重命名为新的变量名。这是可以使用重命名解构语法

```
let music = {
  type: 'rock',
  heat: '50%'
}
let { type: newType, heat: newHeat } = music;
console.log(newType, newHeat); // rock 50%
```
ES6对象解构其他类型
如果在一个对象中的值包含其他的数据类型同样是可以解构出来的。代码如下：
```

let music = {
  type: ['rock', 'pop', 'jazz'],
  heat: '50%',
}
let { type, heat } = music;
console.log(type, heat); // ["rock", "pop", "jazz"] "50%"
```
ES6解构函数参数
这是一个非常实用的功能，可以将传递进来的函数对象参数直接解构为变量，在函数中可以方便调用。这只需要简单的对象解构函数即可完成。代码如下：
```
function getMusic({ type, heat }) {
  console.log(type, heat);
}
getMusic({ type: 'rock', heat: '80%'}); // rock 80%
```
ES6对象字面量增强总结：
在ES6中新增了很多功能（方便书写理解和避免代码问题）。这里学习了对象字面量简写，不用再书写两次代码。还有对象方法简写也是同样道理。接着是字符串模板，一种更优雅的字符串拼接方式。还有对象键运算，他使用了[]可以为对象键赋值时进行一些运算。最后是ES6的解构语法。利用这些新特性我们的代码将会写的更优雅和易于理解。

6.Arrow Functions （箭头函数）in ES6
在ES6中，也有了丰富的箭头函数。这些丰富的箭头是令人惊讶的因为它们将使许多操作变成现实，比如，
以前我们使用闭包，this总是预期之外地产生改变，而箭头函数的迷人之处在于，现在你的this可以按照你的预期使用了，身处箭头函数里面，this还是原来的this。
如下面代码：

```javascript
var f = v => v * v;
```
等同于es5中
```javascript
var f = function(v) {
  return v * v;
}
```
有了箭头函数在ES6中， 我们就不必用that = this或 self =  this  或 _this = this  或.bind(this)。例如，下面的代码用ES5就不是很优雅：
```
var _this = this;
$('.btn').click(function(event){
  _this.sendData();
})
```
在ES6中就不需要用 _this = this：
```
$('.btn').click((event) =>{
  this.sendData();
})
```
不幸的是，ES6委员会决定，以前的function的传递方式也是一个很好的方案，所以它们仍然保留了以前的功能。
注意事项：


下面这是一个另外的例子，我们通过call传递文本给logUpperCase() 函数在ES5中：
```
var logUpperCase = function() {
  var _this = this;
 
  this.string = this.string.toUpperCase();
  return function () {
    return console.log(_this.string);
  }
}
 
logUpperCase.call({ string: 'ES6 rocks' })();
```
而在ES6，我们并不需要用_this浪费时间([call的理解](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call))
```
var logUpperCase = function() {
  this.string = this.string.toUpperCase();
  return () => console.log(this.string);
}
logUpperCase.call({ string: 'ES6 rocks' })();
```
请注意，只要你愿意，在ES6中=>可以混合和匹配老的函数一起使用。当在一行代码中用了箭头函数，它就变成了一个表达式。它将暗地里返回单个语句的结果。如果你超过了一行，将需要明确使用return。

这是用ES5代码创建一个消息数组：
```
var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9'];
var messages = ids.map(function (value) {
  return "ID is " + value; // explicit return
});
```
用ES6是这样：
```
var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9'];
var messages = ids.map(value => `ID is ${value}`); // 隐性的 return
```
请注意，这里用了字符串模板。

在箭头函数中，对于单个参数，括号()是可选的，但当你超过一个参数的时候你就需要他们。

在ES5代码有明确的返回功能：
```
var ids = ['5632953c4e345e145fdf2df8', '563295464e345e145fdf2df9'];
var messages = ids.map(function (value, index, list) {
  return 'ID of ' + index + ' element is ' + value + ' '; // 明确的 return
});
```
在ES6中有更加严谨的版本，参数需要被包含在括号里并且它是隐式的返回：
```
var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9'];
var messages = ids.map((value, index, list) => `ID of ${index} element is ${value} `); // implicit return
```
嵌套的箭头函数
箭头函数内部，还可以再使用箭头函数。下面是一个 ES5 语法的多重嵌套函数。

```
function insert(value) {
  return {into: function (array) {
    return {after: function (afterValue) {
      array.splice(array.indexOf(afterValue) + 1, 0, value);
      return array;
    }};
  }};
}

insert(2).into([1, 3]).after(1); //[1, 2, 3]
```
上面这个函数，可以使用箭头函数改写。

```
let insert = (value) => ({into: (array) => ({after: (afterValue) => {
  array.splice(array.indexOf(afterValue) + 1, 0, value);
  return array;
}})});

insert(2).into([1, 3]).after(1); //[1, 2, 3]
```

7.Promises in ES6
Promises 是一个有争议的话题。因此有许多略微不同的promise 实现语法。Q，bluebird，deferred.js，vow, avow, jquery 一些可以列出名字的。也有人说我们不需要promises，仅仅使用异步，生成器，回调等就够了。但令人高兴的是，在ES6中有标准的Promise实现。

下面是一个简单的用setTimeout()实现的异步延迟加载函数:
```
setTimeout(function(){
  console.log('Yay!');
}, 1000);
```
在ES6中，我们可以用promise重写:
```
var wait1000 =  new Promise(function(resolve, reject) {
  setTimeout(resolve, 1000);
}).then(function() {
  console.log('Yay!');
});
// 或者用箭头函数
var wait1000 =  new Promise((resolve, reject)=> {
  setTimeout(resolve, 1000);
}).then(()=> {
  console.log('Yay!');
});
```
到目前为止，代码的行数从三行增加到五行，并没有任何明显的好处。确实，如果我们有更多的嵌套逻辑在setTimeout()回调函数中，我们将发现更多好处：
```
setTimeout(function(){
  console.log('Yay!');
  setTimeout(function(){
    console.log('Wheeyee!');
  }, 1000)
}, 1000);
```
在ES6中我们可以用promises重写：
```
var wait1000 =  ()=> new Promise((resolve, reject)=> {setTimeout(resolve, 1000)});
wait1000()
    .then(function() {
        console.log('Yay!')
        return wait1000()
    })
    .then(function() {
        console.log('Wheeyee!')
    });
```
还是不确信Promises 比普通回调更好？其实我也不确信，我认为一旦你有回调的想法，那么就没有必要额外增加promises的复杂性。

虽然，ES6 有让人崇拜的Promises 。Promises 是一个有利有弊的回调但是确实是一个好的特性，更多详细的信息关于[promise:Introduction to ES6 Promises](http://jamesknelson.com/grokking-es6-promises-the-four-functions-you-need-to-avoid-callback-hell/).
8.Block-Scoped Constructs Let and Const（块作用域构造Let and Const）
在ES6代码中，你可能已经看到那熟悉的身影let。在ES6里let并不是一个花俏的特性，它是更复杂的。Let是一种新的变量申明方式，它允许你把变量作用域控制在块级里面。我们用大括号定义代码块，在ES5中，块级作用域起不了任何作用：
```
function calculateTotalAmount (vip) {
  var amount = 0;
  if (vip) {
    var amount = 1;
  }
  { // more crazy blocks!
    var amount = 100;
    {
      var amount = 1000;
    }
  }  
  return amount;
}
console.log(calculateTotalAmount(true));
```
结果将返回1000，这真是一个bug。在ES6中，我们用let限制块级作用域。而var是限制函数作用域。
```
function calculateTotalAmount (vip) {
  var amount = 0; 
  if (vip) {
    let amount = 1; 
  } 
  { // more crazy blocks!
    let amount = 100; 
    {
      let amount = 1000; 
    }
  }  
  return amount;
} 
console.log(calculateTotalAmount(true));
```
这个结果将会是0，因为块作用域中有了let。如果（amount=1）.那么这个表达式将返回1。谈到const，就更加容易了；它就是一个不变量，也是块级作用域就像let一样。下面是一个演示，这里有一堆常量，它们互不影响，因为它们属于不同的块级作用域:
```
function calculateTotalAmount (vip) {
  const amount = 0;  
  if (vip) {
    const amount = 1;
  } 
  { // more crazy blocks!
    const amount = 100 ;
    {
      const amount = 1000;
    }
  }  
  return amount;
}
console.log(calculateTotalAmount(true));
```
9.Classes（类） in ES6
如果你喜欢面向对象编程（OOP），那么你将喜爱这个特性。以后写一个类和继承将变得跟在facebook上写一个评论那么容易。

类的创建和使用真是一件令人头疼的事情在过去的ES5中，因为没有一个关键字class （它被保留，但是什么也不能做）。在此之上，大量的继承模型像pseudo classical, classical, functional 更加增加了混乱，JavaScript 之间的宗教战争只会更加火上浇油。

用ES5写一个类，有很多种方法，这里就先不说了。现在就来看看如何用ES6写一个类吧。ES6没有用函数, 而是使用原型实现类。我们创建一个类baseModel ，并且在这个类里定义了一个constructor 和一个 getName()方法：
```
class baseModel {
  constructor(options, data) { // class constructor，node.js 5.6暂时不支持options = {}, data = []这样传参
    this.name = 'Base';
    this.url = 'http://azat.co/api';
    this.data = data;
    this.options = options;
   }
 
    getName() { // class method
        console.log(`Class name: ${this.name}`);
    }
}
```
注意我们对options 和data使用了默认参数值。此外方法名也不需要加function关键字，而且冒号(：)也不需要了。另外一个大的区别就是你不需要分配属性this。现在设置一个属性的值，只需简单的在构造函数中分配。
AccountModel 从类baseModel 中继承而来:
```
class AccountModel extends baseModel {
    constructor(options, data) {
```
为了调用父级构造函数，可以毫不费力的唤起super()用参数传递：
```
super({private: true}, ['32113123123', '524214691']); //call the parent method with super
       this.name = 'Account Model';
       this.url +='/accounts/';
    }
```
如果你想做些更好玩的，你可以把 accountData 设置成一个属性：
```
get accountsData() { //calculated attribute getter
    // ... make XHR
        return this.data;
    }
}
```
那么，你如何调用他们呢？它是非常容易的：
```
let accounts = new AccountModel(5);
accounts.getName();
console.log('Data is %s', accounts.accountsData);
```
结果令人惊讶，输出是：

Class name: Account Model
Data is  32113123123,524214691

10.Modules（模块） in ES6
众所周知，在ES6以前JavaScript并不支持本地的模块。人们想出了AMD，RequireJS，CommonJS以及其它解决方法。现在ES6中可以用模块import 和export 操作了。

在ES5中，你可以在 <script>中直接写可以运行的代码（简称IIFE），或者一些库像AMD。然而在ES6中，你可以用export导入你的类。下面举个例子，在ES5中,module.js有port变量和getAccounts 方法:
```
module.exports = {
  port: 3000,
  getAccounts: function() {
    ...
  }
}
```
在ES5中，main.js需要依赖require(‘module’) 导入module.js：
```
var service = require('module.js');
console.log(service.port); // 3000
```
但在ES6中，我们将用export and import。例如，这是我们用ES6 写的module.js文件库：
```
export var port = 3000;
export function getAccounts(url) {
  ...
}
```
如果用ES6来导入到文件main.js中，我们需用import {name} from 'module’语法，例如：
```
import {port, getAccounts} from 'module';
console.log(port); // 3000
```
或者我们可以在main.js中把整个模块导入, 并命名为 service：
```
import * as service from 'module';
console.log(service.port); // 3000
```
从我个人角度来说，我觉得ES6模块是让人困惑的。但可以肯定的事，它们使语言更加灵活了。
并不是所有的浏览器都支持ES6模块，所以你需要使用一些像jspm去支持ES6模块。
更多的信息和例子关于ES6模块，请看 this text。不管怎样，请写模块化的JavaScript。

-----
ES6总结
这里还有许多ES6的其它特性你可能会使用到，排名不分先后：

1、全新的Math, Number, String, Array 和 Object 方法
2、二进制和八进制数据类型
3、默认参数不定参数扩展运算符
4、Symbols符号
5、tail调用
6、Generators (生成器)
7、New data structures like Map and Set(新的数据构造对像MAP和set)