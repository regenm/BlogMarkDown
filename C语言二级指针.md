---
title: C语言二级指针
date: 2023-10-01 23:55:07
tags: [笔记, c]
---

# C语言的二级指针

## 	1.起因。

​	想记录一下这个我难受的知识点。

​	事情的起因：在自己用C实现简单的数据结构 “ 栈 ”的时候，写了这样一段代码。在后面遍历的时候不论怎么修改都会出现**segmentation fault。** 后来通过debug才发现S的地址从来都没有发生变化。

```c
void pushStack(int dataToStore,myStack S){
    ptrToNode tmp=(myStack)malloc(sizeof(struct MyStack));
    S->nextNode=tmp;
    tmp->lastNode=S;
    tmp->data=dataToStore;
    tmp->nextNode=NULL;
    S= tmp;
}
```

​	以上代码错误的原因是：**S作为一个指针参数被传入函数** 虽然函数的末尾覆盖了S的地址，**但这只是在这个函数开辟的栈空间里，结束后S的地址并不会变化**

## 	2.解决。

### 		1.方法一：

​			在主函数覆盖S的地址

```c
myStack pushStack(int dataToStore,myStack S){
    ptrToNode tmp=(myStack)malloc(sizeof(struct MyStack));
    S->nextNode=tmp;
    tmp->lastNode=S;
    tmp->data=dataToStore;
    tmp->nextNode=NULL;
    return tmp;
}
```

### 	2.方法二：

​			传入二级指针，即可修改二级指针的值（一级指针的地址）

```c
void pushStack(int dataToStore,myStack *S){
    ptrToNode tmp=(myStack)malloc(sizeof(struct MyStack));
    *S->nextNode=tmp;
    tmp->lastNode=*S;
    tmp->data=dataToStore;
    tmp->nextNode=NULL;
    *S= tmp;
}
```



## 	3.收尾。

​		简单的实现栈这个数据结构其实不一定需要用到二级指针，只是我把问题复杂化了。带来了意向不到的难题。不过至少让我将二级指针收入囊中。不得不感叹一下任重道远啊。

