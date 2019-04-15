---
title: 传统及angular上传文件
date: 2017-7-21
tags:
---

最近做了个上传文件的功能

**1.传统方式**

```
<form action="php/upfile.php" method="post" enctype="multipart/form-data" id="uploadform">
<input type="file" name="image"  file-model="images" onchange="angular.element(this).scope().img_upload(this.files)"/>
<input type="submit">
</form>
```
**2.angular上传**

```
<form enctype="multipart/form-data" id="uploadform">
    <input type="text" name="detail">
    <input type="file" name="image"  file-model="images" onchange="angular.element(this).scope().img_upload(this.files)"/>
    <button ng-click="save()">保存</button>
</form>
```

**后台用PHP**

```
<?php

// 休息5秒
sleep(5);
// 输出上传的生成的信息
print_r($_FILES);
//格式如下
/*
Array ( [upFile] => Array ( [name] => 1.jpg [type] => image/jpeg [tmp_name] => D:\wamp\tmp\phpF928.tmp [error] => 0 [size] => 109994 ) )
*/
/*
可以同时接收多个上传的文件, 每多一个文件数组元素就会增加一个
*/


// 1.获取上传文件对应的字典
$dic = $_FILES["image"];

// 2.获取上传文件的名称
$fileName = $dic["name"];
//echo $fileName;

// 3.获取上传文件存储的临时路径
$filePath = $dic["tmp_name"];
//echo $filePath;

// 3.将上传的文件移动到指定文件夹下
move_uploaded_file($filePath, "images/".$fileName);
```