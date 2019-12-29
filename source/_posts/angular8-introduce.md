---
uuid: dd62b600-29fd-11ea-863a-0721425666c2
title: angular8初体验
s: angular8初体验
date: 2019-12-29 13:41:30
tags:
categories:
coauthor: Haonan
---

>本文将会简单介绍angular8环境搭建，脚手架的相关使用以及angular8简单的介绍，为后续的上手提供参考。

##背景：
Angular是google公司开源的一款前端框架，github地址为：https://angular.io/。是目前非常流行的前端框架之一。
Angular（特指angular2.0以后的版本）事实上必须用 TypeScript 来开发，因为它的文档和学习资源几乎全部是面向 TS 的, Angular本身也是TS开发的。


##前置条件：

1,首先如果想要搭建一套angular的环境，我们需要全局安装较新的稳定的node环境，本人采用的是v10.17.0版本，进行环境的搭建

2,全局安装angular-cli，为了和angular8的版本匹配，建议安装脚手架的版本为8.1.3。

`npm install -g @angular/cli@8.1.3`


##ng-cli的使用：

使用angular-cli创建一个angular的app是很简单的，你只需要在你想创建项目的目录下运行`ng new my-app`，即可进行一个简单的项目创建。

同时，对angular不熟悉的人也可以运行`ng help`进行简单的帮助查看。

在项目的开发中，我们往往需要新建一些页面，模块，组建，服务或者管道。
之前我们可能会通过直接新建文件的形式进行添加，但是angular-cli为我们带来了更便捷的添加方式`ng generate <schematic>`或者`ng g <schematic>`。
通过以上命令可以实现快速搭建。具体的使用可以参考[ng generate](https://angular.io/cli/generate)。

angular-cli不仅提供了新建的功能，同时还提供了包括[ng serve](https://angular.io/cli/serve), [ng build](https://angular.io/cli/build), [ng test](https://angular.io/cli/test) 等常用的工程化的相关功能，此处便不一一介绍了。

##angular的api，绑定和生命周期：
###api
使用过angularjs的同学应该都知道，在angularjs中有一些指令非常方便我们进行数据展示，比如ng-show,ng-if,ng-repeat等等，在angular后续的版本中，这些使用会有一些小小的变化，比如我们如果需要判断部分元素的显示与否，现在需要使用`*ngIf=value`来判断。
如果需要展示一个列表，我们需要使用`*ngFor = 'let l of list'`。

###绑定
对于之前常用的事件指令，比如ng-click，ng-change，现在需要使用`(click)=clickEvent()`来进行事件的绑定。

对于数据的绑定，angular使用`<div class="red font14" [class]="changeGreen">14号绿色字</div>`中的[]进行数据绑定。

由以上两个绑定的例子我们不难看出，angular对比angularjs，对原先的双向数据绑定进行了改动。毕竟数据的单项传导在遇到问题是更方便我们进行数据追踪和问题排查。但是这并不意味着angular废弃了双向数据绑定，你依旧可以通过`[(ngModel)] = value`的形式进行双向数据的绑定，从而满足需求。

###生命周期
angular的后续版本中，开始更加强调生命周期的概念，毕竟每个组建从创建，变更到销毁，我们必须足够清楚才能更好的开发。
以下是angular指令生命周期钩子的作用及调用顺序：

1、ngOnChanges - 当数据绑定输入属性的值发生变化时调用

2、ngOnInit - 在第一次 ngOnChanges 后调用

3、ngDoCheck - 自定义的方法，用于检测和处理值的改变

4、ngAfterContentInit - 在组件内容初始化之后调用

5、ngAfterContentChecked - 组件每次检查内容时调用

6、ngAfterViewInit - 组件相应的视图初始化之后调用

7、ngAfterViewChecked - 组件每次检查视图时调用

8、ngOnDestroy - 指令销毁前调用

其中，对数据的操作官方建议我们放在ngOnInit方法中执行，以免带来数据渲染之类的问题。

##总结：

angular是个大而全的框架，他提供了非常丰富的一整套系统供开发者使用，这也是他标榜自己是一个平台而非框架的原因。在简单的写过一些angular的demo过后，对他的使用总的来说感觉还是棒的，要比写angularjs有意思的多。同时，要学习要了解的也有很多，会在之后的使用过程中继续更新。








