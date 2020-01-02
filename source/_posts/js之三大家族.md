---
title: JavaScript之三大家族（offset/scroll/client） 
date: 2015-06-01 19:35:57
tags: javascript
---
三大家族都是以DOM元素节点的属性形式存在的，都是为了获取元素节点更多的信息。

<!--more-->


## offset家族

- 只能取值，不能设置值
- 获取的结果是数值，不是字符
- 获取的时候不需要style

![图解1](http://upload-images.jianshu.io/upload_images/3793508-27a9b36ab2059cbc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

网页可见区域宽： document.body.clientWidth;

网页可见区域高： document.body.clientHeight;

网页可见区域宽： document.body.offsetWidth (包括边线的宽);

网页可见区域高： document.body.offsetHeight (包括边线的宽);

网页正文全文宽： document.body.scrollWidth;

网页正文全文高： document.body.scrollHeight;

网页被卷去的高： document.body.scrollTop;

网页被卷去的左： document.body.scrollLeft;

## offsetWidth 和 offsetHeight

- 获取对象自身的宽度和高度 ，包括内容、边框和内边距，即：offsetWidth = width + border + padding

```
div{
   width: 300px; border-right:2px solid #ccc; padding:10px;
}
```

注意：和 div.style.width 的区别，其只能获取到行内的数值

### offsetLeft 和 offsetTop

- 距离第一个有定位的父级盒子左边和上边的距离
- 父级盒子必须要有定位，如果没有，则最终以body为准！
- **offsetLeft 从父亲的padding开始算 父亲的border不算.即：从子盒子边框到定位父盒子边框的距离。**



## scroll家族

基本概念

网页正文全文宽： document.body.scrollWidth;

网页正文全文高： document.body.scrollHeight;

网页被卷去的高： document.body.scrollTop;

网页被卷去的左： document.body.scrollLeft;

在实际开发中使用比较多的就是scrollTop，如下图： 
![图解2](http://upload-images.jianshu.io/upload_images/3793508-55fef5ae3ce8fd4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 处理scroll家族浏览器适配问题

- ie9+ 和 最新浏览器

   ` window.pageXOffset; （scrollLeft）`

   `window.pageYOffset; （scrollTop）`

- Firefox浏览器 和 其他浏览器

  `document.documentElement.scrollTop;`

- Chrome浏览器 和 没有声明 DTD <DOCTYPE >

  `document.body.scrollTop;`

- 兼容写法

    `var scrollTop = window.pageYOffset || `
 	`document.documentElement.scrollTop || `
	`document.body.scrollTop || 0; `
 	`var scrollLeft = window.pageXOffset ||` 
 	`document.documentElement.scrollLeft ||` `document.body.scrollLeft || 0;`

### scrollTo(x,y)

- 把内容滚动到指定的坐标

- 格式：scrollTo(xpos,ypos)

    - xpos 必需；要在窗口文档显示区左上角显示的文档的 x 坐标；

    - ypos 必需；要在窗口文档显示区左上角显示的文档的 y 坐标 。

- 网页大部分都没有水平滚动条，所以，这个x 不太常用。



## client家族

###1.1 clientWidth和clientHeight

- 网页可见区域宽： document.body.clientWidth;

- 网页可见区域高： document.body.clientHeight;

### 1.2 clientLeft和clientTop

- clientLeft，clientTop

  - 返回的是元素边框的borderWidth，

  - 如果不指定一个边框或者不定位改元素，其值就为0

### 1.3 offset、client和scroll的区别分析

- left和top分析：

   - clientLeft: 左边边框的宽度；clientTop: 上边边框的宽度

   - offsetLeft: 当前元素距离有定位的父盒子左边的距离；offsetTop: 当前元素距离有定位的父盒子上边的距离

  - scrollLeft: 左边滚动的长度; scrollTop: 上边滚动的长度;

- width和height分析

  - clientWidth\/Height: 内容 + 内边距

  - offsetWidth\/Height: 内容 + 内边距 + 边框

  - scrollWidth\/Height: 滚动内容的宽度和高度


![图解3](http://upload-images.jianshu.io/upload_images/3793508-ad52a8a6471de4af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)