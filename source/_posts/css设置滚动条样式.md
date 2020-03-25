---
title: css设置滚动条样式
date: 2019-01-18
tags: css
typora-root-url: ..

---

滚动条可以分为几个部分：滑块、轨道、横竖交界、两端按钮，可通过css修改默认样式

<!-- more -->

###### 滚动条图解

![scrollbar1](/images/css设置滚动条样式/scrollbar1.png)

###### 参数说明

```
::-webkit-scrollbar 	滚动条整体部分
::-webkit-scrollbar-thumb  	滚动条里面的小方块（滑块），能上下或左右移动
::-webkit-scrollbar-track  	滚动条的轨道（里面装有滑块）
::-webkit-scrollbar-button 	滚动条的轨道的两端按钮，允许通过点击微调小方块的位置。
::-webkit-scrollbar-track-piece 除去滑块的轨道部分
::-webkit-scrollbar-corner 	边角，即两个滚动条的交汇处
::-webkit-resizer 	两个滚动条的交汇处上用于通过拖动调整元素大小的小控件
```



###### css样式示例

```css
/* 可滚动区域 */
.scroll {
  width: 200px;
  height: 200px;
  overflow: auto;
}
/* 滚动条整体 */
.scroll::-webkit-scrollbar{
  width:12px;
  height:12px;
}
/* 轨道 */
.scroll::-webkit-scrollbar-track{
  background: #f2f558;
  border-radius:2px;
}
/* 滑块 */
.scroll::-webkit-scrollbar-thumb{
  background: #409eff;
  border-radius:10px;
}
/* 滑块hover */
.scroll::-webkit-scrollbar-thumb:hover{
  background: #f00;
}
/* 横竖交界 */
.scroll::-webkit-scrollbar-corner{
  background: #179a16;
}
```

![scrollbar](/images/css设置滚动条样式/scrollbar.png)

