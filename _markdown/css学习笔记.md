---
title: css学习笔记
date: 2023-12-22 23:46:18
tags: [笔记, css]
categories: [软件技术]
---

# CSS

## 	简介

> ​	
>
> CSS：Cascading Style Sheet 层叠样式表，是一组样式设置的规则，用于控制页面的外观样式。
>
> 广泛用于页面外观美化，布局和定位。

## CSS如何使用

* 嵌入HTML或者JSP

```css
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style>
		p{
			color:blue;
		}
	</style>
</head>
```



* 外部导入（常用，更加方便，便于复制黏贴 :D）

```css
<head>
	<link rel="stylesheet" type="text/css" href="css/eg.css"> 
</head>
```

## 	基本语法

* 选择器：要修饰的对象，例如HTML的各类标签
* 属性名：属于修饰对象的属性
* 属性值：修饰对象的属性的样式取值

```css
<head>
	<style>
		选择器{
			属性名：属性值;
			属性名：属性值;
		}
	</style>
</head>
```

## 选择器

