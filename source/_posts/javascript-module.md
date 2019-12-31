---
uuid: 711a8a50-2b0f-11ea-bf86-77214ed5fd36
title: JavaScript 模块化
s: javascript-module
date: 2019-12-30 22:19:50
tags:
categories:
coauthor: renyahui
---
### 什么是模块化？
   从狭义上讲，一个模块就是一个实现特定功能的文件，将逻辑相关的代码组织到同一包内。广义上讲模块化其实就是一种规范，一种约束，将每个模块通过固定的方式进行引用和暴露，各个模块按照其依赖关系组合，最终插入到主程序中。各个模块之间相对独立，不必担心命名冲突。它可以将系统代码划分为一系列职责单一，提高系统的可维护性
   
### 为什么要模块化？
早期前端页面只实现了简单的页面交互逻辑，但随着Ajax技术的广泛应用，前端实现了主动向服务器发送请求并操作返回数据的能力，前端页面逻辑越来越多。就会出现函数名冲突，过多的全局变量，依赖关系不好管理的问题，模块化很好的解决了这些问题，它可以使代码低耦合，提高了系统的可维护性，通过引用也提高了系统的可复用性。 

### JS模块化规范
## 1.CommonJS
CommonJS 的一个模块就是一个脚本文件，通过执行该文件来加载模块。CommonJS 规范规定，每个模块内部，module 变量代表当前模块。这个变量是一个对象，它的 exports 属性（即 module.exports）是对外的接口。加载某个模块，其实是加载该模块的 module.exports 属性
例如：
```
math.js:
    exports.add = function(a, b){`
       return a+b;
    }

b.js:
    var math = require('math.js');
    console.log(math.add(1,2))//3
   ```
  
NodeJS 的模块系统，就是基于 CommonJS 规范实现的，CommonJS 模块规范适合服务端编程。Commonjs规范具有以下特点：
* 原生Module对象，每个文件都是一个Module实例
* 文件内通过require对象引入指定模块
* 所有文件加载均是同步完成
* 通过module关键字暴露内容
* 模块可以多次加载

## 2.AMD和RequireJS
**AMD**

Commonjs基于Node原生api在服务端可以实现模块同步加载，但是仅仅局限于服务端，客户端如果同步加载依赖的话时间消耗非常大，所以需要一个在客户端上基于Commonjs但是对于加载模块做改进的方案，于是AMD规范诞生了。AMD采用异步方式加载模块，所有依赖这个模块的语句，都定义在一个回调函数中，等到所有依赖加载完成之后，这个回调函数才会运行。所以，它要求有两个参数
`require([module], callback);`
callback即为加载成功后的的回调函数
例如：
```
require(['math'], function (math) {
     math.add(1,2)
});
```
**RequireJS**

RequireJs是js模块化的工具框架，是AMD规范的具体实现。
RequireJs有两个最鲜明的特点：
* 依赖前置：动态创建`<script>`引入依赖，在`<script>`标签的onload事件监听文件加载完毕；一个模块的回调函数必须得等到所有依赖都加载完毕之后，才可执行。

* 配置文件：有一个main文件，配置不同模块的路径，以及shim不满足AMD规范的js文件。
```
<script src="js/require.js" data-main="js/main"></script>

require.config({
  shim:{
  },
  paths: {
    "a": "/a.js", 
    "b": "/b.js",
  }
});
require(["a","b"],function($,_){
});
```

## 3.CMD和SeaJs
AMD使用前置依赖，提前执行，在定义模块的时候就要声明其依赖的模块,而CMD使用就近依赖，只有在用到某个模块的时候才会require。SeaJs则是CMD规范的实现。
```
// 定义模块 a.js
define(function(require, exports, module) {
  console.log("a")
});

// 加载模块
seajs.use(['/a.js'], function(my){
});

```
## 4.ES6 Module
在es6之前，以上规都是出自个大公司或者社区，es6在语言的标准上实现了模块功能，而且实现相对简单，成为浏览器和服务器的通用解决方案。其主要由两个命令构成:export和import,export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。
```
/** 定义模块 math.js **/
var basicNum = 0;
var add = function (a, b) {
    return a + b;
};
export { basicNum, add };

/** 引用模块 **/
import { basicNum, add } from './math';
function test(ele) {
    ele.textContent = add(99 + basicNum);
}
```
