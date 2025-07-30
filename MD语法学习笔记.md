---
title: MD语法学习笔记
date: 2023-09-16 23:47:12
tags: [笔记, md]
---

# 					MD语法学习笔记

## 1.基本符号：

​	md文档中基本标记都是基于 \* - +. > 这几个符号的组合操作

## 2.标题。

​	MD总共有六级标题

```markdown
# 标题1 # 
## 标题2 ##
### 标题3 ###
#### 标题4 ####
##### 标题5 #####
###### 标题6 #######
```

# 标题1 # 
## 标题2 ##
### 标题3 ###
#### 标题4 ####
##### 标题5 #####
###### 标题6 #######



## 3.列表。

+ 无序列表

```markdown
+ a
+ b
+ c
 
- d
- e
- f
 
* g
* h
* i
```

+ a
+ b
+ c

- d
- e
- f

* g
* h
* i

+ 有序列表

```markdown
有序列表
1. abc
2. abc
3. dada
 
错序列表
2. awfa
5. awef
25. dfaf
```



有序列表
1. abc
2. abc
3. dada

错序列表
2. awfa
5. awef
25. dfaf

+ 嵌套列表

```markdown
无序
+ a
  + a1
  + a2
+ ba
+ c
 
有序
1. a
   1. adac
      1. adaw
   2. adfsda
2. ad
3. sad
```

无序
+ a
  + a1
  + a2
+ ba
+ c

有序
1. a
   1. adac
      1. adaw
   2. adfsda
2. ad
3. sad



## 4.引用块。

+ 引用

```markdown
引用
> 引用1
引用1 引用1
 
```

引用
> 引用1
> 引用1 引用1

+ 嵌套引用

```
 嵌套引用
> 嵌套引用1 
>> 嵌套引用1 
```

 嵌套引用
> 嵌套引用1 
> > 嵌套引用1 

## 5.代码块。

+ 单行

```
代码块
`
adaawf
`
```

代码块
`
adaawf
`

+ 多行代码块

~~~markdown

```
adaawfwrfwr
```
~~~

```
adaawfwrfwr
```

## 6.链接。

```markdown
[BAIDU](www.baidu.com)
```

[BAIDU](www.baidu.com)

## 7.图片。

```markdown
![图片](https://note.youdao.com/favicon.ico)
```

![图片](https://note.youdao.com/favicon.ico)

## 8.	分割线。

```markdown
分割线
---
- - -
-------
*****
* * *
____
```

- - -
-------
*****
* * *
____

## 9.表格。

```
表格
|123|234|345|
|:-|:-:|-:|
|abc|bcd|cde|
|abc|bcd|cde|
|abc|bcd|cde|
|abc|bcd|cde|
|abc|bcd|cde|
//例子二
|123|234|345|
|:---|:---:|---:|
|abc|bcd|cde|
|abc|bcd|cde|
|abc|bcd|cde|
//例子三
123|234|345
:-|:-:|-:
abc|bcd|cde
abc|bcd|cde
abc|bcd|cde
```

表格
| 123      |  234  |  345 |
| :------- | :---: | ---: |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| //例子二 |       |      |
| 123      |  234  |  345 |
| :---     | :---: | ---: |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| //例子三 |       |      |
| 123      |  234  |  345 |
| :-       |  :-:  |   -: |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |
| abc      |  bcd  |  cde |

## 10.字体。

```markdown
//斜体
*md*
 
//粗体
**md**
 
//斜体
_md_
 
//斜体
__md__
 
//转义
 
\+
 
//删除线
 
~~删除~~
```

//斜体
*md*

//粗体
**md**

//斜体
_md_

//斜体
__md__

//转义

\+

//删除线

~~删除~~

## 11.表情！:smile:

:santa:



```markdown
格式
:内容描述:
例如：
:smile:
```

