---
title: 如何实现div填充父容器的剩余宽度空间
date: 2016-04-10 18:45:19
tags:
---

前段时间因项目需要，出现了一个有点棘手的问题，那就是**如何实现div填充父容器的剩余宽度空间**。
![](http://i.imgur.com/2lTgF9X.png)
<!--more-->
下面给出一个奇技淫巧：

    <div class="box">
    	<div class="left">固定宽度</div>
    	<div class="right">自动填充宽度</div>
	</div>
 

----------

    .box{
            width: 500px;
            height: 250px;
            border: 2px solid #000;
            color: #fff;
        }
        .left{
            width: 200px;
            height: 200px;
            background-color: red;
            float: left;//关键
        }
        .right{
            height:200px;
            background-color: blue;
			overflow: hidden;//关键
        }

即左边盒子**左浮float:left**，右边盒子设置**overflow:hidden;**