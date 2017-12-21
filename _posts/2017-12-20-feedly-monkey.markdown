---
layout: post
title: "利用油猴脚本去除 feedly 中的广告"
data: 2017-12-19 13:24:02
description: 
category: 油猴脚本 feedly css tampermonkey
tags: [油猴脚本 feedly css tampermonkey]
---

一年多来一直是 feedly 的重度用户，订阅了上百个 rss feed，包括 hacker news, 各种技术blog, 新闻网站等等。
最近，估计是为了盈利，feedly 在每个 rss 阅读列表里都加上了广告链接，而且伪装的和正常阅读内容极为相像，很难分辨。
虽然不介意为服务付费，但 feedly 的付费功能对我基本没什么吸引力，为了提升自己的阅读体验，作为一个对 web 一知半解的程序员，开始想办法去除页面中的广告。

油猴脚本，英文名叫 tampermonkey, 可以自己写一些 javascript 脚本，在 url 满足指定的 pattern 页面中自动运行。
之前我就用这个写了个适配百度百科页面的脚本，节省了我很多时间，这次当然也准备用它来做个过滤 feedly 页面的脚本。

首先在 chrome 中右键 inspect 某一个广告 block, 看了看 html 代码，发现每一个广告都是 ```<div class="entry unread u4 density-29"``` 的形式。
似乎很简单，新建了个油猴脚本，在里面写上

```
	$(".entry.unread.u4.density-29").remove();
```

保存之后，刷新了下 feedly, 没有任何变化。怎么回事？仔细观察了下 feedly 的载入过程，发现阅读列表是在整个页面加载完成之后延迟载入了。
前面说过，我是一个对 web 一知半解的程序员，面对这种情况，我抓瞎了，不知道怎么指定上面那条移除指令的运行时机。

突然，我想到一个好主意，虽然不知道应该在什么时间点移除广告，但我可以预先修改掉 CSS 让广告元素隐藏掉啊，这样，无论广告是什么时候载入的，都会受到 CSS 的影响。
于是，经过一番搜索和粘贴，我写下了如下代码

```
function addGlobalStyle(css) {
    var head, style;
    head = document.getElementsByTagName('head')[0];
    if (!head) { return; }
    style = document.createElement('style');
    style.type = 'text/css';
    style.innerHTML = css;
    head.appendChild(style);
}

addGlobalStyle('.entry.unread.u4.density-29 { display: none ; }');
```

再次刷新页面，这次终于起效果了，然而，不但广告，连正常的文章都不见了，懵b的我看了看正常文章的 html 代码，发现它们同样是```<div class="entry unread u4 density-29"```。
再次对比了下正常文章和广告的 ```div```, 发现正常文章的 ```div```中包含很多额外的 ```attribute````, 而广告中没有。于是，搜索了下 CSS 中属性匹配的规则，我改动了下代码

```
addGlobalStyle('.entry.unread.u4.density-29:not([data-dot]) { display: none ; }');
```

这次终于正常工作了，又重回了干净清爽无广告的 feedly。