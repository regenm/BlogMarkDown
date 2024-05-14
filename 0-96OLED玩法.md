---
title: 0.96OLED玩法
date: 2024-05-14 19:08:23
tags: [project,STM32,IIC,OLED,notes,笔记]
---

基于0.96寸OLED，主控ssd1306

# 显示原理

基于数据手册

## 硬件特性

每个像素点独立控制，通过写入现存的二进制数据进行每个像素的熄灭和点亮

* 像素

    **128*64**

## 名词定义

```python
 # 0.96 OLED
 '''
 |********************************|
 |********************************|
 |********************************|
 |********************************|
 |********************************|
 '''
 # 模拟图
```

* SEG

相当于一列：一列**64**个像素

* COLOMN

相当于一行：一行**128**个像素

* COMMON

相当于一行：一行**128**个像素

* PAGE

相当于8行：**128*8**个像素

## 显示方式（ssd1306）

> There are 3 different memory addressing mode in SSD1306: page addressing mode, horizontal addressing mode and vertical addressing mode. 

### 1. page addressing mode(页地址模式)

> Page addressing mode (A[1:0]=10xb) 
>
> In page addressing mode, after the display RAM is read/written, the column address pointer is increased automatically by 1. 
>
> If the column address pointer reaches column end address, the column address pointer is reset to column start address and ==page address pointer is not changed.== 
>
> Users have to set the new page and column addresses in order to access the next page RAM content The sequence of movement of the PAGE and column address point for page addressing mode is shown in Figure 10-1. 

![page addressing mode](/images/OLED/1.png)

一次写`1Byte(8位)`，与`horizontal addressing mode`的区别只是**PAGE是否自动改变**

### 2.horizontal addressing mode(水平地址模式)

> Horizontal addressing mode (A[1:0]=00b)
>
> In horizontal addressing mode, after the display RAM is read/written, the column address pointer is increased automatically by 1. 
>
> If the column address pointer reaches column end address, the column address pointer is reset to column start address and ==page address pointer is increased by 1==. 
>
> The sequence of movement of the page and column address point for horizontal addressing mode is shown in Figure 10-3. 
>
> When both column and page address pointers reach the end address, the pointers are reset to column start address and page start address (Dotted line in Figure 10-3.) 

![horizontal addressing mode](/images/OLED/2.png)

一次写`1Byte(8位)`

### 3.vertical addressing mode(垂直地址模式)

> Vertical addressing mode: (A[1:0]=01b)
>
> In vertical addressing mode, after the display RAM is read/written, the page address pointer is increased automatically by 1. 
>
> If the page address pointer reaches the page end address, ==the page address pointer is reset== to page start address and column address pointer is increased by 1. 
>
> The sequence of movement of the page and column address point for vertical addressing mode is shown in Figure 10-4. 
>
> When both column and page address pointers reach the end address, the pointers are reset to column start address and page start address 
>
> (Dotted line in Figure 10-4.) 

![vertical addressing mode](/images/OLED/3.png)

### 总结(我的理解)

三种模式都是一次竖着写`1Byte(8位)`，

1. 第一种向右延伸，到终点不回来
2. 第二种向右延伸，到终点自动回来并+1到下一行
3. 第三种向下延伸，到终点COL自动+1到下一列

# 玩法

## 1.显示基本ASCII

## 2.显示中文

## 3.显示BMP位图
