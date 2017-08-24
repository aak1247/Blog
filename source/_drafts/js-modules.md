title: JavaScript 的模块化

category: JavaScript

tags: 
    - js
    - 笔记

date: 2017/07/29

---

可以说，模块儿是使得JavaScript成为一个优秀的开发语言的直接前提，它被用于本地和后端开发等都是在有了模块儿化之后的事情。JS的模块儿化有几个比较常用的标准，如AMD和CommonJS, 最常用的是CommonJS规范，CommonJS是一个同步加载方案，其实现有NodeJs，AMD是一个异步加载方案，其实现有RequireJS。另外，ES6标准也对模块化进行了支持，与前面两个标准不同的是，ES6为编译时加载，便于静态优化、动态绑定等。

# CommonJS

## 引入
```js
var react = require('react');
```

## 导出
```js
module.exports = function(){
    this.say = function(){
        console.log("module");
    };
};
//或
exports.say = function(){
    console.log("module");
}
```

这两种方式的区别是``exports``导出的为一个对象（包含添加给它的各种属性），而``module.exports``导出的可以是任何变量，如一个``number``。


# AMD

## 引入

```js
requirs(['jquery','underscore'], function(){
    console.log("import jquery and underscore");
});
```

## 导出

```js

```