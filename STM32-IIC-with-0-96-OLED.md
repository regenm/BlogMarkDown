---
title: STM32 IIC with 0.96 OLED
date: 2024-05-06 17:24:10
tags: [project ,STM32,IIC,OLED,notes,笔记]
---

# IIC 通信原理

## 基本介绍
[点击跳转](link to be added)

## 原理介绍

### Init
#### 硬件层
	需要接上拉电阻（保证传输稳定性）

#### 代码

'''c

void IIC_Init(){
	IIC_SCL=1;
	IIC_SDA=1;
	
}



'''




### Start

#### 物理层
	IIC总线的SCL保持高电平，SDA由高电平变为低电平后，延时(>4.7us)，SCL变为低电平。
* 步骤
1. SCL 高
2. SDA 高->低 + 延时（>4.7us）
3. SCL 高->低


#### 代码
'''c

void IIC_Start(){
	IIC_SCL=1;
	IIC_SDA=1;
	delay_us(5);
	IIC_SDA=0;
	delay_us(5);
	IIC_SCL=0;
	
} 

'''

### Tramsmit Data

#### 物理层
	传输时，SDA的宽度应该大于SCL的宽度，在SCL为高时读取SDA，SCL为低时改变SDA的电平。
	==注意：==传输数据过程总不允许SDA变化（否则会被视为开始或停止信号）
* 步骤
1. 拉低SCL进行数据传输
2. SDA传送高低电平（0或1）
3. SCL在高电平时期读取SDA的数据
4. 响应信号
#### 代码

'''c

void IIC_Transmit(u8 data){
	IIC_SCL=0;  // 
	u8 i=0;
	for(i=0;i<8;i++){
		if(data & 0x80 ){ 
			IIC_SDA=1;	    // transmit 1
			delay_us(2);
			IIC_SCL=1;
			delay_us(2);
			IIC_SCL=0;
			delay_us(2);
		}
		else{
			IIC_SDA=0;  	   // tramsmit 0	
			delay_us(2);
			IIC_SCL=1;
			delay_us(2);
			IIC_SCL=0;
			delay_us(2);
		}
		data<<=1;
	}

}



'''

### Stop
#### 物理层
	与Start相反：SCL为高时SDA由低到高
1. SCL = 1
2. SDA = 0
3. SDA = 1 + 延时(>4.7us)


#### 
### Response




# STM32 实现


## 标准库

## hal库


