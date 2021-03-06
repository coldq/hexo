---
title: 关于正则的一些记录
date: 2017-05-26 19:33:41
tags: 
  - RegExp
categories: JS
---

## 
![1](http://lanrsp.oss-cn-shanghai.aliyuncs.com/111.jpg)

### 1. 字符串选择

The dog chased the cat中单词 the，我们可以使用正则表达式: /the/gi,我们可以把这个正则表达式分成几段：

	/ 是这个正则表达式的头部

	the 是我们想要匹配的模式

	/ 是这个正则表达式的尾部

	g 代表着 global(全局)，意味着返回所有的匹配而不仅仅是第一个。
	
	i 代表着忽略大小写，意思是当我们寻找匹配的字符串的时候忽略掉字母的大小写。
    

### 2. 数字选择

在JavaScript中, 数字选择器类似于: /\d/g。

在选择器后面添加一个加号标记(+)，例如：/\d+/g，它允许这个正则表达式匹配一个或更多数字。

例如：

```
var testString = "There are 34 cats but 4 dogs.";

var expression = /\d+/g; 
result:[ '34', '4' ]

var expression = /\d/g; 
result:[ '3', '4', '4' ]
```

尾部的g是'global'的简写，意思是允许这个正则表达式 找到所有的匹配而不是仅仅找到第一个匹配。

```js
var testString = "There are 3 cats but 4 dogs.";
var expression = /\d+/g;  // Change this line

var digitCount = testString.match(expression);//.length;

console.log(digitCount)

[ '3', '4' ]
[Finished in 0.1s]
```

### 3. 选择反转

你可以用正则表达式选择器的大写版本 来转化任何匹配。
举个例子：\s 匹配任何空白字符，\S 匹配任何非空白字符。

### 4. 子串

现在有如下一串字符串：
    "asdfkjasldjkf"shiner"df
 
需求：
     需要提取出shiner子字符串。
 
命令如下：
    echo "asdfkjasldjkf\"shiner\"df" | sed 's/\(.*\)"\(.*\)"\(.*\)/\2/g'
    shiner

命令解释
    s： 表示替换命令
    \(.*\)" : 表示第一个引号前的内容
    "\(.*\)"：表示两引号之间的内容
    )"\(.*\)：表示引号后的内容
    \2: 表示第二对括号里面的内容
    括号里的表达式匹配的内容，可以用\1，\2等进行引用，第n个括号对内的内容，就用\n引用。
 
这个命令的意思是：
用\2代表的第二个括号的内容（shiner）去替换整个字符串，这样就得到了我们所需要的子字符串了。
