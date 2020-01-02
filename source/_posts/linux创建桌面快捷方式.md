---
title: linux在桌面上创建应用程序快捷方式
date: 2017-10-09 14:15:30
tags: Linux
---
系统：CentOS 6.4 桌面环境：gnome
本文以eclipse为例，使用**命令行**创建桌面快捷方式

<!--more-->

桌面快捷方式文件放置于**/usr/share/applications**或者**~/.local/share/applications**处。前一个目录存储的桌面快捷方式每个用户都可以使用，而后一个目录则含有仅仅为特定用户创建的快捷方式。

要为特定程序或命令创建桌面快捷方式，你可以使用任意文本编辑器创建一个**.desktop**文件，然后把它放到**/usr/share/applications**或者**~/.local/share/applications**处。一个典型的**.desktop**文件如下所示。


```
[Desktop Entry]
Encoding=UTF-8
Version=1.0                                     #version 
Name[en_US]=yEd                                 #name of an app.
GenericName=GUI Port Scanner                    #longer name of an app.
Exec=java -jar /opt/yed-3.11.1/yed.jar          #command used to launch an app.
Terminal=false                                  #whether an app requires to be run in a terminal
Icon[en_US]=/opt/yed-3.11.1/icons/yicon32.png   #location of icon file.
Type=Application                                #type
Categories=Application;Network;Security;        #categories in which this app should be listed.
Comment[en_US]=yEd Graph Editor                 #comment which appears as a tooltip.
```
## 1.在/usr/share/applications下创建Eclipse.desktop文件 ##
`touch /usr/share/applications Eclipse.desktop`

## 2.用任何编辑器编辑Eclipse.desktop文件，写入以下内容 ##

```
[Desktop Entry]
Name=eclipse                             #快捷方式显示名称
Name[zh_CN]=Eclipse                      #中文名称
Comment=eclipse Client                   #鼠标悬停时说明
Exec=/usr/local/eclipse/eclipse          #应用程序的绝对路径
Icon=/usr/local/eclipse/icon.xpm         #应用程序图标的绝对路径
Terminal=false                           #是否用命令行启动
Type=Application                         #类型为程序
Categories=Application;                  #分类
Encoding=UTF-8                           #编码
```
写入时要特别注意，**后面不能有空格**，否则会不识别，出现这种情况：
![](/images/快捷方式1.png)

编辑完，保存后会在**/usr/share/applications**这个文件夹下出现eclipse的图标
![](/images/快捷方式2.png)
此时双击这个图标就可以启动Eclipse了！

## 3. 将这个图标拷贝到桌面上##

`cp /usr/share/applications/Eclipse.desktop /home/username/桌面`

命令中**username**换成你自己的名字
拷贝完成后可能会出现如下情况：
![](/images/快捷方式3.png)
双击后弹出提示：
![](/images/快捷方式4.png)
这是由于权限的问题，所以修改权限

`chmod 777 /home/username/桌面/Eclipse.desktop`

修改后就大功告成了！
