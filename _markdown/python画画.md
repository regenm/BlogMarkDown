---
title: python画画
date: 2023-10-06 14:55:02
tags: [笔记, python, turtle]
categories: [软件技术]
---

## 	起因

​		学校在国庆节和中秋节中举办了一个编程比赛：内容是在一个小时内用编程语言画出节日符号；例如月饼和国旗之类的。假期闲来无事，于是就浅浅参加了一下。

## 	使用python的TURTLE库

​		说要用编程画画，我立马就想到了用python ，毕竟人生苦短，我用python。其实主要还是不想用字符串来在终端输出，过于麻烦。

## 		基础知识![Python Turtle Coordinates New](https://files.realpython.com/media/Turtle_EDIT_Graph.790c213ce0ba.jpg)

			* 中心点在screen中心
			* (0,0) 即 Home
			* 默认初始化画笔向右

```python
>>> t.right(90)   
>>> t.forward(100)
>>> t.left(90)
>>> t.backward(100)
```

![Python Turtle Moving Updated](https://files.realpython.com/media/Update_-_Moving_Turtle_VIDEO_GIF.61623cf40fed.gif)

### 	内置图形

```python
 t.circle(60)
 t.dot(20)
```

### 	背景颜色

```python
turtle.bgcolor("blue")
```

### 	画笔的粗细

```python
>>> t.pensize(5)
>>> t.forward(100)
```

### 	填充颜色

```python
>>> t.begin_fill()
>>> t.fd(100)
>>> t.lt(120)
>>> t.fd(100)
>>> t.lt(120)
>>> t.fd(100)
>>> t.end_fill()
```

![Python Turtle Begin Fill End Fill New](https://files.realpython.com/media/Turtle_Begin_End_Fill_GIF.849f73374a22.gif)

### 	绘制速度

```python
>>> t.speed(1)
>>> t.forward(100)
>>> t.speed(10)
>>> t.forward(100)
```

### 	撤回

```
>>> t.undo()
```

### 	克隆

```python
>>> c = t.clone()
>>> t.color("magenta")
>>> c.color("red")
>>> t.circle(100)
>>> c.circle(60)
```

## 以上就是python Turtle模组的基本语法

​	但是光会这些基本知识也不行，你还得有亿点点艺术细胞。毕竟它作为画画工具有一点简朴了。
