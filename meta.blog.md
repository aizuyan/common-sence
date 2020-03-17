# meta 标签详解

## 引言

`<meta>` 元素提供有关页面的元信息，放在文档的头部，不包含内容，属性定义了与文档相关的键值对，不会显示在页面上，但对于机器是可读的。

通常情况下，meta 元素被用于规定页面的描述、关键字、文档的作者、最后修改时间等。

本篇文章将会分析各大网站的 meta 内容，然后统计高频的，然后进行整理、通读。

## IE 渲染模式<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
上面这个可能是，碰到最多的一个 meta 标签了，天猫、淘宝、京东、百度都有类似的标签（部分没有`,chrome=1`）。

这个 meta 标签是用来设置浏览器的兼容性模式的，是 IE8 新加的一个属性，对于 < IE8 的浏览器是不识别的，也是 IE 内核特有的属性，其他内核浏览器不认识。

content 中的内容`IE=edge,chrome=1`，其中`IE=edge`告诉浏览器，以当前浏览器最高版本 IE 来进行渲染；`chrome=1`告诉浏览器，如果安装了 [Google Chrome Frame](https://en.wikipedia.org/wiki/Google_Chrome_Frame)插件（GCF），则保持 IE 外观的模式下，使用 chrome 内核进行渲染，这里注意，该插件支持 IE6 ~ IE9，这里读者朋友可能会有疑惑了，不是 < IE8 的浏览器不支持当前小节的属性吗，那怎么使用呢？其实也很简单，在网址前面加上`gcf:`即可，比如`gcf:https://baidu.com`。

## 视窗设置 <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no"/>


```html
<meta http-equiv="" content="">
```
上面这个meta标签经常使用，可能读者也用过，但让你给他下个定义，你可能不一定说不出来。

一般情况，`http-equiv`用来模拟 http 返回头信息。

如果 http 返回头信息中和 meta 标签中都设置了，优先使用 http 返回头信息中的内容，忽略 meta 标签中的内容

## <meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1" >
`X-UA-Compatible` 用来指定浏览器兼容性模式设置，是 IE8 新加的一个设置，对于 < IE8 的浏览器是不识别的。

但是，IE8 中可能会有下面的用法，让 IE8 浏览器按照 IE7 的模式渲染。
```
<meta http-equiv="X-UA-Compatible" content="IE=7">
```

`<meta http-equiv="X-UA-Compatible" content="edge" />`

Edge 模式通知 IE 浏览器，以最高级别的可用模式显示内容，这实际上破坏了“锁定”模式。即如果你有IE9的话说明你有IE789，那么就调用高版本的那个也就是IE9

<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1" >

如果IE有安装Google Chrome Frame，那么就走安装的组件，如果没有就和 <meta http-equiv="X-UA-Compatible" content="edge" />一样。

