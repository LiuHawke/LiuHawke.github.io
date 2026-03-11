---
title: "Unity-中英对照汉化"
date: 2022-05-09T15:56:17+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---

## 演示

![在这里插入图片描述](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192014911.png)



## 操作

把汉化包zh-cn.po用记事本打开，复制内容到word编辑

word软件主窗口
编辑–替换，打开查找替换窗口

点击高级，选中使用通配符

查找内容：```(msgid\ ")(*)("^13msgstr ")(*)("^13)```



替换为：```\1\2\3\2 \4\5```

全部替换完之后，复制全部内容到记事本
记事本中文件–另存为zh-cn.po就OK了

## 解释

### 举例

### 汉化包中的一段内容

\#: Editor/Mono/Inspector/AudioMixerControllerInspector.cs:1
msgid " Threshold Volume"
msgstr “阈值音量”

查找内容
msgid " Threshold Volume"
msgstr “阈值音量”
替换为
msgid " Threshold Volume"
msgstr " Threshold Volume阈值音量"

### 拆分解释

(msgid\ ")(*)("13)(msgstr ")(\*)("13)
（）括号内为需要查找的文本内容，一个括号对应一个替换文本的元素即，
第一个(msgid\ ")对应替换\1
第二个(*)对应\2
(”^13)对应\3
以此类推

(msgid\ ")这一段在查找中会找到第二行的msgid "
需要查找的msgid "之中有个空格，需要用\来避免编译

(msgid\ ")(*)("^13)这一段有三个元素
(*)代表所有文本
(”^13)代表"加上一个换行
这一段就表示找到msgid "开头，然后到"行末端结尾的一整行
并且分为了3个元素，在替换的时候用\序号调用

