---
uuid: 4b0b77b0-29df-11ea-87fb-4bcf6a36d102
title: Using Promises
s: Using Promises
date: 2019-12-29 10:02:40
tags:
  - promise
  - javascript
categories:
coauthor: lichun
---
本篇主要阐述如何使用  *promise* 的链式调用，从以下几点说起：

- 经典回调地狱

    通常，我们可能会有这样的场景：我们创建一个文件，创建成功时做一些事情，失败时做一些事情。那么假设有这样一个异步创建文件的函数  *createFile()*，它有两个回调函数，一个回调函数是文件成功创建时的回调，另一个则是出现异常时的回调。如下：
    ```
    function successCallback(result) {
        console.log("create file success: " + result);
    }
        
    function failureCallback(error) {
        console.log("create file failure: " + error);
    }
        
    createFile(args, successCallback, failureCallback);
    ```
    因此，按照原来这种回调方式，在之前如果要想做多重的异步操作，有可能导致以下经典的回调地狱：
    ```
    doSomething(function(result) {
        doSecondThing(result, function(newResult) {
            doThirdThing(newResult, function(finalResult) {
                console.log('Got the final result: ' + finalResult);
            }, failureCallback);
        }, failureCallback);
    }, failureCallback);
    ```

- 使用  *promise* 链
    
    先看这段代码：
    ```
    var promise = doSomething();
    var promise2 = promise.then(successCallback, failureCallback);
    ```
    
    上述*promise2*不仅表示*doSomething*函数的完成，也代表了传入的成功或者失败的*callback*的完成，两个*callback*有可能返回一个*Promise*对象，从而形成另一个异步操作。
    所以由*promise2*新增的回调函数都会被依次排在由上一个*successCallback*或*failureCallback*执行后所返回的*Promise*对象的后面。
    
    为了避免第一部分提到的经典回调地狱，利用上述特性，我们可以把回调绑定到被返回的*Promise*上，形成一个*Promise*链：
    ```
    doSomething().then(function(result) {
        return doSecondThing(result);
    })
    .then(function(newResult) {
        return doThirdThing(newResult);
    })
    .then(function(finalResult) {
        console.log('Got the final result: ' + finalResult);
    })
    .catch(failureCallback);
    ```
    上述代码中，捕获错误函数里的  *catch(failureCallback)*  实际是 *then(null,failureCallback)*  的缩略形式，且每个.*then()*一定要有返回值，否则*callback*将无法获取上一个*Promise*的结果。
    
    通常在代码执行过程中，一遇到异常抛出，*Promise*链就会停下来，直接调用链式中最近的*catch*处理程序来继续当前执行。
    
    另外，关于promise链我们有以下约定：
              
        1.在本轮Javascript event loop（事件循环）运行完成 之前，回调函数是不会被调用的;
                  
        2.通过then()添加的回调函数总会被调用，即便它是在异步操作完成之后才被添加的函数。
                  
        3.通过多次调用then()，可以添加多个回调函数，它们会按照插入顺序一个接一个独立执行。
        
- *catch* 的后续链式操作： 
 
    有可能会在链式操作中抛出一个失败之后，还继续进行下面的操作，也就是在使用一个*catch*之后，继续执行*promise*。
    
    示例：
    
    ```
    new Promise((resolve, reject) => {
        console.log('init');
        resolve();
    })
    .then(() => {
        throw new Error('some error');
        console.log('not execute');
    })
    .catch(() => {
        console.log('execute catch');
    })
    .then(() => {
        console.log('execute after catch');
    });
    
    ```
    输出结果：
    ```
    init
    execute catch
    execute after catch
    ```

- 嵌套以及  *catch* 捕获错误的作用域限制
    
    先看以下示例：
    ```
    doOneThing().then(() => doAnotherThing()
        .then((anotherResult) => {
            doSomethingElse(anotherResult)
        })
        .catch((e) => {
            console.log("another failure" + e.message)
        })
    )
    .then(() => doSomethingAfterOne())
    .catch((e) => {
        console.log("One failure: " + e.message)}
    );
    ```
    上述例子中，在第一处的*catch*只会捕获到*doAnotherThing*和*doSomethingElse*函数的错误。第二处的*catch*捕捉外部错误，诸如*doOneThing*以及*doSomethingAfterOne*等的函数错误。
    且即使在第一处捕获到错误，后面的函数也会继续运行下去。
    
    若能够合理利用*catch*的作用域，可实现高精度的错误捕获和修复。                         

- 多个  *promise* 的并行操作

    1.可以使用*Promise.all()*，然后等多个操作全部结束后再进行下一步操作：                 
    ```
    Promise.all([func1(), func2(), func3()])
    .then(([result1, result2, result3]) => { /* use result1, result2 and result3 */ });
    ```
    2.如果需要保证执行的顺序，可使用：
    ```
    [func1, func2, func3].reduce((p, f) => p.then(f), Promise.resolve())
    .then(result3 => { /* use result3 */ });
    ```
    上述代码中，*p* 为上一次调用回调返回的值，或者是提供的初始值(*initialValue*)，*f* 为数组正在处理中的元素。
    相当于：*Promise.resolve().then(func1).then(func2).then(func3);*


- 最后，提出几个使用 *promise* 链需要注意的点

        1.在then()里一定要有返回值。否则promise链条就会断了，不能保证函数同步执行；
         
        2.不要使用不必要的嵌套。嵌套使用不当，可能会导致未捕获的错误；
        
        3.不要忘了catch。若不使用catch，程序执行出错，难以发现错误的根源。而且在大多数浏览器中不能终止的Promise链里的rejection。
    

   本篇参考自MDN文档，谢谢阅读。
