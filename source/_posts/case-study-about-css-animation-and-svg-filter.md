---
uuid: 45613120-2cbd-11ea-b9c9-f5f7a77d9114
title: CSS动画与SVG滤镜的结合应用
s: case-study-about-css-animation-and-svg-filter
date: 2020-01-01 21:36:41
tags:
    - CSS
    - SVG
categories:
    - Technology
coauthor: lidong
---
本文出自对一个CASE的分解，CASE来源: https://codepen.io/z-/pen/zYxdRQy  

### 源码分析
页面部分：
```pug
div.main
        div.footer
            div.bubbles
                - for (var i = 0; i < 128; i++) //Small numbers looks nice too
                    div.bubble(style=`--size:${2+Math.random()*4}rem; --distance:${6+Math.random()*4}rem; --position:${-5+Math.random()*110}%; --time:${2+Math.random()*2}s; --delay:${-1*(2+Math.random()*2)}s;`)
            div.content
        svg(style="position:fixed; top:100vh")
            defs
                filter#blob
                    feGaussianBlur(in="SourceGraphic" stdDeviation="10" result="blur")
                    feColorMatrix(in="blur" mode="matrix" values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -9" result="blob")
                    feComposite(in="SourceGraphic" in2="blob" operator="atop")
```
页面部分的结构很清晰，主要关注四个元素`div.bubbles`，`dic.content`，`svg`，以及在`svg`中定义的`filter#blob`，相关的CSS(非完整代码)：  
```scss
.bubbles {
    position: absolute;
    top:0;
    left:0;
    right:0;
    filter:url("#blob");
    .bubble {
        position: absolute;
        left:var(--position, 50%);
        background:var(--footer-background);
        border-radius:100%;
        animation:bubble-size var(--time, 4s) ease-in infinite var(--delay, 0s),
                  bubble-move var(--time, 4s) ease-in infinite var(--delay, 0s);
        transform:translate(-50%, 100%);
    }
}       
@keyframes bubble-size {}
@keyframes bubble-move {}
```
结合两部分的源码，在此案例中使用到的相关技术有：
1. pug模版 (相关文档：https://pugjs.org/zh-cn/api/getting-started.html)
2. CSS `animation`
3. SVG `filter`

同时也可以看出作者的设计思路：
1. 确定单形`div.bubble`，设置`border-radius:100%;`，将单形变为圆形
2. 为单形添加`animation`，`transform`，实现动态效果
3. 为单形配置`svg filter#blob`，使单形在动画过程中表现更加平滑
4. 复制多个单形于`div.bubbles`中，并使用`div.content`作为遮罩层
  
### 相关技术
#### CSS animation
CSS animation使界面元素可以从一个样式配置切换为另一个样式配置。用户需要指定动画执行的重要属性，及每个动画执行过程中关键帧的样式规则。  
关键的属性：  
1. `animation-name`指定一个应用的一系列动画，简单来说就是可以自定义动画序列，通过`@keyframes`，如上面的`bubble-move`。
```scss
@keyframes bubble-move {
    from {
        bottom:-4rem;
    }
    to {
        bottom:var(--distance, 10rem);
    }
}
// 也可以设置百分比做更细致的指定
```
2. `animation-duration`属性指定一个动画周期的时长。
3. `animation-timing-function`属性定义CSS动画在每一动画周期中执行的节奏，即在动画过程中对于动画渲染的速度做了规定，也可以设置自定义函数`<timing-function>`。详细的信息可以参考这篇文档：https://www.oreilly.com/library/view/creating-web-animations/9781491957509/ch04.html
   > A timing function is a function that alters the speed at which your properties animate.
4. `animation-delay`属性定义动画于何时开始，即从动画应用在元素上到动画开始的这段时间的长度。默认值为0，即动画应用即开始

#### CSS translation
CSS动画既然提到了`animation`，那就不能不说一说`translation`。两者之间的相同点在于，都可以用于设置元素的平滑过渡，不同之处在于`animation`注重元素本身在预设时间段所展示的动态效果，而`translation`更注重与用户的交互后被触发而展示动态效果，这也表现在CSS中两者的设置上。`animation`的设置是一种预定设置，设置了`duration`，`delay`，`timing-function`，关键帧也是固定的。`translation`会设置在该元素上有哪些属性如果发生变化会被影响，针对每个属性变化可以设置`duration`，`timing-function`。一个应用场景就是，在一个元素上设置`translation`指定不同属性变化的处理逻辑，然后在用户操作响应的CSS上做不同动态响应处理。  
```scss
div.translation {
    widows: 10rem;
    height: 20rem;
    background-color: green;
    transition: width 2s ease-in-out, height 1s ease, background-color 2ms linear;

    &:hover {
        widows: 100rem;
        height: 200rem;
        background-color: red;
    }
}
```
> 可在MDN上查到当前支持的可动画属性列表

#### SVG filter
如果只是应用上述CSS，是无法做到CASE中的复杂效果。SVG的`filter`为CASE做了最后一部分工作，用滤镜加了层美颜。引用一个博客中对SVG滤镜工作流程的解释：  
>SVG在使用了滤镜的元素里，不会将原始图形直接渲染出来，而是会将原始图形的像素信息渲染到临时位图中，然后由`filter元素`指定的操作会被应用到这个临时位图，最终把计算结果渲染为最终图形输出。  

SVG滤镜的核心就在于filter元素指定的操作，就CASE中用到的操作，做一简单说明：
1. 首先滤镜接收到`单形`后，使用`feGaussianBlur`为其生成了模糊效果。`stdDeviation`指定模糊程度，值越大模糊就越强，输入两个值，可以在x，y轴方向上分别指定模糊程度。
2. `feColorMatrix`可以允许修改任意像素点的颜色及阿尔法值，当type设置为`matrix`时，values 为20个数字信息，以CASE中信息为例，所表达含义如下：  
    R  | G | B | A | CONST
    ---|---|---|---|------
    1  | 0 | 0 | 0 | 0
    0  | 1 | 0 | 0 | 0
    0  | 0 | 1 | 0 | 0
    0  | 0 | 0 | 19| -9  

    原始图形每一像素的颜色值(一个表示为[红,绿,蓝,透明度] 的矢量)，都经过矩阵乘法计算出的新颜色。values由上至下的四组数值分别会计算出新颜色的R，G，B，A。例如：`新颜色的R = 1*R + 0*G + 0*B + 0*A + CONST`，其余类似。了解了计算规则之后，从设置的数值可以发现，在计算新颜色的时候R，G，B三原色并未做修改，保持了`单形`的颜色，但是在计算阿尔法通道值时做了设置，这也是最终界面`单形`呈现不规则形状的根本原因。
3. 以上两个步骤都输出了一份图层，`feComposite`接收两个图层，设置`operator`属性确定该如何合并，常见的合并方式有：  
    `over` 生成的结果就是a层叠在b上面  
    `in` 典型的蒙版效果  
    `out` 反转蒙版的效果  
    `atop` a的一部分在b里面，b的一部分在a外面  
    `xor` 包含b的外面的a的部分和a的外面的b的部分  
    `arithmetic` 可以提供4个参数，k1 k2 k3 k4，每个像素的每个阿尔法通道结果按照这个方式计算：`k1*a*b + k2*a +k3*b +k4`

除此之外，介绍另外几个常用的滤镜操作：  
1. `feImage`可以接收图片输出图层
2. `feOffset`将元素作位置上的偏移，经常用于`阴影`的形成
3. `feMerge`合并多个图层

总结一下，SVG滤镜的开发机制类似于PS绘图，通过各种操作生成图层，合并图层后输出，对于每个操作的设置和对图形的影响需要格外熟悉。SVG滤镜这次只是做了简单的研究，后续若遇到新奇的操作再加说明。

### 延伸设计
后面把文章支持上传本地图片，GIF的功能添加上之后，再补充贴图。