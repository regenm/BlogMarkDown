---
title: Data Structure and Algorithm
date: 2023-11-12 21:58:40
tags: [笔记, Data-Structure-and-Algorithm]
---

# Start

## 树

### 哈夫曼树（最优二叉树）

> ​		哈夫曼算法：
>
> 1. 构造 n 棵二叉树森林，每一个都是带权值的根节点。
> 2. 选择权值最小的两棵树作为左右子树，其根节点的权值为左右子树权值之和。
> 3. 删除这两棵树，将新的树加入森林。
> 4. 重复操作到只剩下一棵树为止。

```c++
/*
*	基于结构体数组的哈曼树
*	C++
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef double DataType; //结点权值的数据类型

typedef struct HTNode //单个结点的信息
{
	DataType weight; //权值
	int parent; //父节点
	int lc, rc; //左右孩子
}*HuffmanTree;

typedef char **HuffmanCode; //字符指针数组中存储的元素类型
						  //用于存储哈夫曼编码

//在下标为1到i-1的范围找到权值最小的两个值的下标，其中s1的权值小于s2的权值
void Select(HuffmanTree& HT, int n, int& s1, int& s2)
{
	int min;
	//找第一个最小值
	for (int i = 1; i <= n; i++)
	{
		if (HT[i].parent == 0)
		{
			min = i;
			break;
		}
	}
	for (int i = min + 1; i <= n; i++)
	{
		if (HT[i].parent == 0 && HT[i].weight < HT[min].weight)
			min = i;
	}
	s1 = min; //第一个最小值给s1
	//找第二个最小值
	for (int i = 1; i <= n; i++)
	{
		if (HT[i].parent == 0 && i != s1)
		{
			min = i;
			break;
		}
	}
	for (int i = min + 1; i <= n; i++)
	{
		if (HT[i].parent == 0 && HT[i].weight < HT[min].weight&&i != s1)
			min = i;
	}
	s2 = min; //第二个最小值给s2
}

//构建哈夫曼树
void CreateHuff(HuffmanTree& HT, DataType* w, int n)
{
	int m = 2 * n - 1; //哈夫曼树总结点数
    
	HT = (HuffmanTree)calloc(m + 1, sizeof(HTNode)); //开m+1个HTNode，因为下标为0的HTNode不存储数据
    
	for (int i = 1; i <= n; i++)
	{
		HT[i].weight = w[i - 1]; //赋权值给n个叶子结点
	}
    
	for (int i = n + 1; i <= m; i++) //构建哈夫曼树
	{
		//选择权值最小的s1和s2，生成它们的父结点
		int s1, s2;
		Select(HT, i - 1, s1, s2); //在下标为1到i-1的范围找到权值最小的两个值的下标，其中s1的权值小于s2的权值
		HT[i].weight = HT[s1].weight + HT[s2].weight; //i的权重是s1和s2的权重之和
		HT[s1].parent = i; //s1的父亲是i
		HT[s2].parent = i; //s2的父亲是i
		HT[i].lc = s1; //左孩子是s1
		HT[i].rc = s2; //右孩子是s2
	}
    
	//打印哈夫曼树中各结点之间的关系
	printf("哈夫曼树为:>\n");
	printf("下标   权值     父结点   左孩子   右孩子\n");
	printf("0                                  \n");
	for (int i = 1; i <= m; i++)
	{
		printf("%-4d   %-6.2lf   %-6d   %-6d   %-6d\n", i, HT[i].weight, HT[i].parent, HT[i].lc, HT[i].rc);
	}
	printf("\n");
}

//生成哈夫曼编码
void HuffCoding(HuffmanTree& HT, HuffmanCode& HC, int n)
{
	HC = (HuffmanCode)malloc(sizeof(char*)*(n + 1)); //开n+1个空间，因为下标为0的空间不用
	char* code = (char*)malloc(sizeof(char)*n); //辅助空间，编码最长为n(最长时，前n-1个用于存储数据，最后1个用于存放'\0')
	code[n - 1] = '\0'; //辅助空间最后一个位置为'\0'
	for (int i = 1; i <= n; i++)
	{
		int start = n - 1; //每次生成数据的哈夫曼编码之前，先将start指针指向'\0'
		int c = i; //正在进行的第i个数据的编码
		int p = HT[c].parent; //找到该数据的父结点
		while (p) //直到父结点为0，即父结点为根结点时，停止
		{
			if (HT[p].lc == c) //如果该结点是其父结点的左孩子，则编码为0，否则为1
				code[--start] = '0';
			else
				code[--start] = '1';
			c = p; //继续往上进行编码
			p = HT[c].parent; //c的父结点
		}
		HC[i] = (char*)malloc(sizeof(char)*(n - start)); //开辟用于存储编码的内存空间
		strcpy(HC[i], &code[start]); //将编码拷贝到字符指针数组中的相应位置
	}
	free(code); //释放辅助空间
}

//主函数
int main()
{
	int n = 0;
	printf("请输入数据个数:>");
	scanf("%d", &n);
	DataType* w = (DataType*)malloc(sizeof(DataType)*n);
	if (w == NULL)
	{
		printf("malloc fail\n");
		exit(-1);
	}
	printf("请输入数据:>");
	for (int i = 0; i < n; i++)
	{
		scanf("%lf", &w[i]);
	}
	HuffmanTree HT;
	CreateHuff(HT, w, n); //构建哈夫曼树

	HuffmanCode HC;
	HuffCoding(HT, HC, n); //构建哈夫曼编码

	for (int i = 1; i <= n; i++) //打印哈夫曼编码
	{
		printf("数据%.2lf的编码为:%s\n", HT[i].weight, HC[i]);
	}
	free(w);
	return 0;
}


```

### 二叉树：求树的高度

1. 递归

```c
/*
使用递归
*/

int GetHeight(BinTree BT){
    if(!BT) return 0;
    return max(GetHeight(BT->Left),GetHeight(BT->Right))+1;
} 
int max(int a,int b){return a > b ? a : b;}
```

2. 使用队列

```c
//使用队列

int GetHeight( BinTree BT )
{
    if(BT==NULL)return 0;
    BinTree arr[100];
    BinTree tmpFront;
    int front=0,rear=0;
    int h=0;
    arr[rear++]=BT;
    int count=1;
    int nextCount=1;
    while(front!=rear){
        h++;
        count=nextCount;
        nextCount=0;
        while(count--){
            tmpFront=arr[front++];
            if(tmpFront->Left){
                arr[rear++]=tmpFront->Left;
                nextCount++;
            }
            if(tmpFront->Right){
                arr[rear++]=tmpFront->Right;
                nextCount++;
            } 
        }
    }
    return h;
    
}
```



### 二叉树：顺序存储

​	存储按照完全二叉树来（遇到空节点则 赋值 isEmpty 为 true）

```c
#define MaxSize 100

struct TreeNode{
   ElemType value; //结点中的数据元素
   bool isEmpty;   //结点是否为空
}

int main(){
   TreeNode t[MaxSize];
   for (int i=0; i<MaxSize; i++){
      t[i].isEmpty = true;
   }
}

```

### 二叉树的创建

```c
//输入:ABC##DE#G##F###

void creat(BiTree *T)	//创建二叉树
{
	char ch;
	scanf("%c",&ch);
	if(ch=='#')		//判断该字符ch是不是为'#',意思是该节点是不是空的
		*T=NULL;
	else
	{
		*T=(BiTree)malloc(sizeof(BiTNode));	//为结点T申请空间
		(*T)->data=ch;		//将ch的值赋给(*T)->data
		creat(&(*T)->lchild);
		creat(&(*T)->rchild);
	}
    
}
```





### 二叉树的遍历



* **二叉树：先序遍历**

```c
int PreOrder(BiTree T){
   if(T!=NULL){
      visit(T);                 //访问根结点
       
      PreOrder(T->lchild);      //递归遍历左子树
      PreOrder(T->rchild);      //递归遍历右子树
   }
    else{
        return 0;				//遍历失败，根节点空
    }
    return 1;
}     
```

**二叉树：中序遍历**

```c
int ShowZhongXu(BitTree T)			//		先序遍历二叉树
{
	if(T==NULL)						//	递归中遇到NULL，返回上一层节点
	{
		return 0;
	}
	else {
	ShowZhongXu(T->lchild);			//	递归遍历左子树
	printf("%d ",T->data);
	ShowZhongXu(T->rchild);			//	递归遍历右子树
    }
    return 1;
}
```

**二叉树：后序遍历**

```c
int ShowZhongXu(BitTree T)			//		先序遍历二叉树
{
	if(T==NULL)						//	递归中遇到NULL，返回上一层节点
	{
		return 0;					// 空根
	}
	else {
	ShowZhongXu(T->lchild);			//	递归遍历左子树
	ShowZhongXu(T->rchild);			//	递归遍历右子树
    printf("%d ",T->data);			
    }
    return 1;
}
```

- **二叉树：层序遍历**

* 使用队列

```c
void LevelOrder(BiTree T){
   LinkQueue Q;
   InitQueue (Q);          //初始化辅助队列
    
   BiTree p;
   EnQueue(Q,T);           //将根节点入队
    
   while(!isEmpty(Q)){     //队列不空则循环
      DeQueue(Q,p);        //队头结点出队
      visit(p);            //访问出队结点
      if(p->lchild != NULL)
         EnQueue(Q,p->lchild);   //左孩子入队
      if(p->rchild != NULL)
         EnQueue(Q,p->rchild);   //右孩子入队
   }
}
```

### 二叉树交换左右孩子

```c
//类似于先序遍历
void swap(BiTree T)
{
	if(T!=NULL)
	{
		BiTNode *m=T->lchild;/*这三行类似于"打擂台法"*/
		T->lchild=T->rchild;
		T->rchild=m;			
		swap(T->lchild);
		swap(T->rchild);
	}
}
```

### 求二叉树高度(深度)

```c
int Depth( BiTree T )
{
    int countl =0;
    int countr =0;
    int max;
    if(T==NULL){
        return 0;
    }
    else
    {
       
        countl=Depth( T->lchild );
        countr=Depth( T->rchild );
        max=countl>countr? countl:countr;
        return max+1;
    }
}
```



### 线索二叉树

> 将空的指针域用以指向前驱后继节点。
>
> 遵循：
>
> - ltag==0，指向左孩子；ltag==1，指向前驱结点
> - rtag==0，指向右孩子；rtag==1，指向后继结点

		1. 二叉树的线索化

```c
//中序线索化
void inOrderThreadTree(Node* node)
{
	//如果当前结点为NULL 直接返回
	if (node == NULL) {
		return;
	}
	//先处理左子树
	inOrderThreadTree(node->left_node);
	if (node->left_node == NULL)
	{
		//设置前驱结点
		node->left_type = 1;
		node->left_node = pre;
	}
	//如果结点的右子节点为NULL 处理前驱的右指针
	if (pre !=NULL && pre->right_node == NULL)
	{
		//设置后继
		pre->right_node = node;
		pre->right_type = 1;
	}
	//每处理一个节点 当前结点是下一个节点的前驱
	pre = node;
	//最后处理右子树
	inOrderThreadTree(node->right_node);
}

```

2. 中序遍历线索二叉树

```c
void inOrderTraverse(Node* root)
{
	//从根节点开始先找到最左边
	if (root == NULL)
	{
		return;
	}
	Node* temp = root;
	//先找到最左边结点 然后根据线索化直接向右遍历
	while (temp != NULL && temp->left_type == 0)
	{
		temp = temp->left_node;
	}
	while (temp != NULL)
	{
		//输出
		temp = temp->right_node;
	}
}
```

### 二叉搜索树

**线索二叉树操作集合**

1. 结构体

```c
typedef struct TNode *Position;
typedef Position BinTree;
struct TNode{
    ElementType Data;
    BinTree Left;
    BinTree Right;
};
```

2. 主函数

```c
#include <stdio.h>
#include <stdlib.h>

typedef int ElementType;
typedef struct TNode *Position;
typedef Position BinTree;
struct TNode{
    ElementType Data;
    BinTree Left;
    BinTree Right;
};

void PreorderTraversal( BinTree BT ); /* 先序遍历，由裁判实现，细节不表 */
void InorderTraversal( BinTree BT );  /* 中序遍历，由裁判实现，细节不表 */

BinTree Insert( BinTree BST, ElementType X );
BinTree Delete( BinTree BST, ElementType X );
Position Find( BinTree BST, ElementType X );
Position FindMin( BinTree BST );
Position FindMax( BinTree BST );

int main()
{
    BinTree BST, MinP, MaxP, Tmp;
    ElementType X;
    int N, i;

    BST = NULL;
    scanf("%d", &N);
    for ( i=0; i<N; i++ ) {
        scanf("%d", &X);
        BST = Insert(BST, X);
    }
    printf("Preorder:"); PreorderTraversal(BST); printf("\n");
    MinP = FindMin(BST);
    MaxP = FindMax(BST);
    scanf("%d", &N);
    for( i=0; i<N; i++ ) {
        scanf("%d", &X);
        Tmp = Find(BST, X);
        if (Tmp == NULL) printf("%d is not found\n", X);
        else {
            printf("%d is found\n", Tmp->Data);
            if (Tmp==MinP) printf("%d is the smallest key\n", Tmp->Data);
            if (Tmp==MaxP) printf("%d is the largest key\n", Tmp->Data);
        }
    }
    scanf("%d", &N);
    for( i=0; i<N; i++ ) {
        scanf("%d", &X);
        BST = Delete(BST, X);
    }
    printf("Inorder:"); InorderTraversal(BST); printf("\n");

    return 0;
}
/* 你的代码将被嵌在这里 */
```

3. 操作集合

```c
//插入
BinTree Insert( BinTree BST, ElementType X ){
    if(BST==NULL) {          /* 若原树为空，生成并返回一个结点的二叉搜索树 */
        BST = (BinTree)malloc(sizeof(BinTree));
        BST ->Data = X;
        BST ->Left = BST ->Right = NULL;
    }else {         /* 开始寻找要插入元素的位置 */
        if(X < BST ->Data ) {
            BST ->Left = Insert(BST ->Left, X);
        }else if(X > BST ->Data ) {
            BST ->Right = Insert(BST ->Right, X);
        }
        /* X已经存在，不用操作 */
    }
    return BST;
} 
//删除
BinTree Delete( BinTree BST, ElementType X ){
    BinTree Tmp;
    if(BST==NULL)    
        printf("Not Found\n");
    else {
        if( X < BST->Data)  
            BST ->Left = Delete(BST->Left, X);          /* 左子树递归删除 */
        else if(X > BST->Data ) 
            BST ->Right = Delete(BST->Right , X);       /* 右子树递归删除*/
        else {                                          /* 找到需要删除的结点 */
            if(BST->Left && BST->Right) {               /* 被删除的结点有左右子结点 */
                Tmp=FindMin(BST->Right);                /* 在右子树中找到最小结点填充删除结点 */
                BST->Data = Tmp ->Data;
                BST->Right=Delete(BST->Right,BST->Data);/* 递归删除要删除结点的右子树中最小元素 */
            }else {                                     /* 被删除结点有一个或没有子结点*/
                Tmp = BST;
                if(!BST->Left) BST = BST->Right;        /*有右孩子或者没孩子*/ 
                else if(!BST->Right)    BST = BST->Left;/*有左孩子，一定要加else，不然BST可能是NULL，会段错误*/ 
                free(Tmp);                              /*如无左右孩子直接删除*/
            }
        }
    }
    return BST;
}
//查找
Position Find( BinTree BST, ElementType X ){
    if(BST==NULL)    return NULL;
    if(BST->Data==X)    return BST; 
    if(X>BST->Data)     return Find(BST->Right,X);      
    if(X<BST->Data)     return Find(BST->Left,X);
 
    /*  以下几种写法均可，推荐第上面这一种 
 
    if(!BST)    return NULL;
    if(BST->Data==X)    return BST; 
    if(X>BST->Data)     Find(BST->Right,X);     
    if(X<BST->Data)     Find(BST->Left,X);
 
    if(BST){
        if(BST->Data==X)    return BST; 
        if(X>BST->Data)     Find(BST->Right,X);     //如果不写return，则返回过来的值并没有继续返回给最开始的函数 
        if(X<BST->Data)     Find(BST->Left,X);
    } 
    else return NULL;   
 
    if(BST){
        if(BST->Data==X)    return BST; 
        if(X>BST->Data)     return  Find(BST->Right,X); 
        if(X<BST->Data)     return  Find(BST->Left,X);
    } 
    return NULL;
 
    if(BST){
        if(BST->Data==X)    return BST; 
        if(X>BST->Data)     return Find(BST->Right,X);      
        if(X<BST->Data)     return Find(BST->Left,X);
    } 
    else return NULL;
    */                          
}
/*如果return NULL前面不写else且Find前也不写else，则最后递归返回的也没return，最后只能是执行到了return NULL
返回了，而如果find 前加上了return则就把递归的结果利用起来了，最后加不加else也无所谓了，而如果直接最后else，
不加return find也是可以的，加上了else之后就不会被每一次返回时最后的return NULL给覆盖掉，所以也行。 */ 
Position FindMin( BinTree BST ){
    if(BST){
        while(BST->Left){
            BST=BST->Left;
        }
    } 
    return BST; 
} 
Position FindMax( BinTree BST ){
    if(BST){
        while(BST->Right){
            BST=BST->Right;
        }
    } 
    return BST; 
}
```



## 图

### 并查集

​	 	主要用于解决一些**元素分组**的问题。也可以用来判断图的连通性，它管理一系列**不相交的集合**，并支持两种操作：

- **合并**（Union）：把两个不相交的集合合并为一个集合。
- **查询**（Find）：查询两个元素是否在同一个集合中。

```c
int find(int x)	//最简单版本
{
    /*
    *当数据很多时，查找会很耗费时间
    *
    */
    if(father[x]!=x) father[x]=find(father[x]);
    return father[x];
}

int find(int x){           //查找根节点的函数 
    /*
    *	升级版
    *	带路径压缩功能
    */
    int a;
	a=x;
	while(x!=father[x])x=father[x];
	
	while(a!=father[a]){    //路径压缩 
		int z=a;
		a=father[a];
		father[z]=x;
	}
	return x;
}
void Union(int a,int b){ 
    						//合并两个集合
    int fx=find(a);
    int fy=find(b);
    father[fx]=fy;
}

int judgeConnect(){
    						//判断连通性
    int i,k=0;
    for(i=1;i<=vertex;i++)
        if(father[i]==i) k++;
        if(k==1) return 1;
    else return 0;
    
}
```

### AOE图

> *concept:*
>
> 1、**最早发生**时间：从前往后，前驱结点到当前结点所需时间，取**最大值**；
>
> 2、**最迟发生**时间：从后往前，后继结点的最迟时间减去边权的值，取**最小值**；
>
> **结束节点的最早发生时间和最迟发生时间相同。**
>
> 3、关键路径：最早发生时间和最迟发生时间相同的结点叫做关键路径上的结点；
>
> 4、**最早开始**时间：等于当前边起始节点的最早发生时间；
>
> 5、**最晚开始**时间：等于当前便指向结点的最迟时间减去当前边的权值；
>
> 6、**最早完工**时间：等于当前边指向结点的最早发生时间；
>
> 7、**最晚完工**时间：等于当前边指向结点的最迟发生时间；

### 图的链式存储结构

```c
#include <stdio.h>
#include <stdlib.h>
#define MVNum 100                                 //最大顶点数 
typedef struct ArcNode{                        //表结点 
    int adjvex;                                    //邻接点的位置 
    struct ArcNode * nextarc;      //指向下一个表结点的指针 
  }ArcNode; 
  
typedef struct VNode{ 
   char data;                                    //顶点信息 
   ArcNode * firstarc;         //指向第一个表结点的指针 
}VNode, AdjList[MVNum];                 //AdjList表示邻接表类型 

typedef struct{ 
    AdjList vertices;              //头结点数组
    int vexnum, arcnum;     //图的当前顶点数和边数 
}ALGraph; 

void CreatMGraph(ALGraph *G);/* 创建图 */
void printGraph(ALGraph G);/*输出图 */
int main()
{
    ALGraph G;
    CreatMGraph(&G);
    printGraph(G);
    return 0;
}
void CreatMGraph(ALGraph *G)
{
    int i,j,k;
    ArcNode *s;
    scanf("%d%d",&G->vexnum,&G->arcnum);
    getchar();
    for(i=0;i<G->vexnum;i++)
         scanf("%c",&G->vertices[i].data);

    for(i=0;i<G->vexnum;i++)
         G->vertices[i].firstarc=NULL;

    for(k=0;k<G->arcnum;k++) {  
        scanf("%d%d",&i,&j);    
        s=(ArcNode*)malloc(sizeof(ArcNode));
        s->adjvex=j;
        s->nextarc=G->vertices[i].firstarc;

        G->vertices[i].firstarc=s;   

        s=(ArcNode*)malloc(sizeof(ArcNode));
        s->adjvex=i;

        s->nextarc=G->vertices[j].firstarc;;
        G->vertices[j].firstarc=s;  
    }
}
void printGraph(ALGraph G)
{
    int i,j;
    ArcNode *p;
    for(i=0;i<G.vexnum;i++)
    {
       printf("%c:",G.vertices[i].data);
       for(p=G.vertices[i].firstarc;p;p=p->nextarc)
           printf(" %c",G.vertices[p->adjvex].data);
       printf("\n");
    }
}
```

## 查找（搜索）

### 折半搜索

> 因为折半查找需要方便地定位查找区域，所以它要求线性表必须具有随机存取的特性。因此，该查找法仅适合于顺序存储结构，不适合于链式存储结构，且要求元素按关键字有序排列。

```c
#include<stdio.h>

int BinSearch(int array[], int x, int n);

int main()
{
    int arr[10];//0 1 2 3 4 5 6 7 8 9 

    int num;
    for(int i=0;i<10;i++)
    {
        arr[i]=i;
    }
    int re=BinSearch(arr,9,10);
    printf("下标：%d\n",re);
    return 0;
}
								//按折半查找法查找值为x的数组元素，若找到则返回x在数组中的下标位置，否则返回-1
int BinSearch(int array[], int x, int n)
{
    int low = 0, high = n-1, mid;//区间左端点low置为0，右端点high置为n-1
    int num=0;
    while (low <= high)			//若左端点小于等于右端点，则继续查找
    {
        num++;
        mid = low + (high - low) / 2;//取数据区间的重点
        printf("%d\n",mid);
        if (x > array[mid])
            low = mid + 1;			//若x>array[mid]，则修改区间的左端点
        else if (x < array[mid])
            high = mid - 1;			//若x<array[mid]，则修改区间的右端点
        else{
            printf("次数：%d\n",num);
            return mid;				//若找到，则返回下标值mid
            }
    }
    return -1;						//循环结束仍未找到，则返回值-1
}
```

### 插值查找

> 类似于折半搜索，只是mid的计算方法不一样
>
> 比较元素的下标：
>
> Mid = Begin + ( (End - Begin) / (A[End] - A[Begin]) ) * (X - A[Begin])
>
> >式子中，各部分的含义分别是：
> >
> >Mid：计算得出的元素的位置；
> >
> >End：搜索区域内最后一个元素所在的位置；
> >
> >Begin：搜索区域内第一个元素所在的位置；
> >
> >X：要查找的目标元素；
> >
> >A[]：表示整个待搜索序列

C语言实现过程

​	**递归算法**

```c
#include <stdio.h>
//实现插值查找算法，ele 表示要查找的目标元素，[begin,end] 指定查找区域
int interpolation_search(int* arr, int begin, int end, int ele) {
    int mid = 0;
    //如果[begin,end] 不存在，返回 -1
    if (begin > end) {
        return -1;
    }
    //如果搜索区域内只有一个元素，判断其是否为目标元素
    if (begin == end) {
        if (ele == arr[begin]) {
            return begin;
        }
        //如果该元素非目标元素，则查找失败
        return -1;
    }
    // 找到"中间元素"所在的位置
    mid = begin + ((end - begin) / (arr[end] - arr[begin]) * (ele - arr[begin]));
    //递归的出口
    if (ele == arr[mid]) {
        return mid;
    }
    //比较 ele 和 arr[mid] 的值，缩小 ele 可能存在的区域
    if (ele < arr[mid]) {
        //新的搜索区域为 [begin,mid-1]
        return interpolation_search(arr, begin, mid - 1, ele);
    }
    else {
        //新的搜索区域为 [mid+1,end]
        return interpolation_search(arr, mid + 1, end, ele);
    }
}
int main()
{
    int arr[10] = { 1,2,3,4,5,6,7,8,9,10 };
    //输出元素 2 所在位置的下标
    int pos = interpolation_search(arr, 0, 9, 2);
    if (pos != -1) {
        printf("%d", interpolation_search(arr, 0, 9, 2));
    }
    else {
        printf("查找失败");
    }
    return 0;
}
```

### 二叉排序树

> 1. 若左子树非空，则左子树上所有结点的值均小于根结点的值。
> 2. 若右子树非空，则右子树上所有结点的值均大于根结点的值。
> 3. 左、右子树也分别是一棵二叉排序树。

```c
/*二叉树的二叉链表结点结构定义*/
typedef struct BiTNode
{
	int data;	//结点数据
	struct BiTNode *lchild, *rchild;	//左右孩子指针
} BiTNode, *Bitree;

/*
递归查找二叉排序树T中是否存在key
指针f指向T的双亲，其初始调用值为NULL
若查找成功，则指针p指向该数据元素结点，并返回TRUE
否则指针p指向查找路径上访问的最后一个结点并返回FALSE
*/
bool SearchBST(BiTree T, int key, BiTree f, BiTree *p){
	if(!T){
		*p = f;
		return FALSE;
	}else if(key == T->data){
		//查找成功
		*p = T;
		return TRUE;
	}else if(key < T->data){
		return SearchBST(T->lchild, key, T, p);	//在左子树继续查找
	}else{
		return SearchBST(T->rchild, key, T, p);	//在右子树继续查找
	}
}

/*
当二叉排序树T中不存在关键字等于key的数据元素时
插入key并返回TRUE，否则返回FALSE
*/
bool InsertBST(BiTree *T, int key){
	BiTree p, s;
	if(!SearchBST(*T, key, NULL, &p)){
		//查找不成功
		s = (BiTree)malloc(sizeof(BiTNode));
		s->data = key;
		s->lchild = s->rchild = NULL;
		if(!p){
			*T = s;	//插入s为新的根节点
		}else if(key < p->data){
			p->lchild = s;	//插入s为左孩子
		}else{
			p->rchild = s;	//插入s为右孩子
		}
		return TRUE;
		}else{
			return FALSE;	//树种已有关键字相同的结点，不再插入
		}
}

/*********************构造二叉排序树************/
int i;
int a[10] = {62, 88, 58, 47, 35, 73, 51, 99, 37, 93};
BiTree T = NULL;
for(i = 0; i<10; i++){
	InsertBST(&T, a[i]);
}
/************************************************/

/*
若二叉排序树T中存在关键字等于key的数据元素时，则删除该数据元素结点，
并返回TRUE;否则返回FALSE
*/
bool DeleteBST(BiTree *T, int key){
	if(!T){
		return FALSE; 
	}else{
		if(key == T->data){
			//找到关键字等于key的数据元素
			return Delete(T);
		}else if(key < T -> data){
			return DeleteBST(T -> lchild, key);
		}else{
			return DeleteBST(T -> rchild, key);
		}
	}
}

```

### 哈希表（散列表）

> ​	散列表是根据关键字而直接进行访问的数据结构。也就是说，散列表建立了关键字和存储地址之间的一种直接映射关系。
>
> ​	这种对应关系称为散列函数，又称为哈希(Hash) 函数。按这个思想，采用散列技术将记录存储在一块连续的存储空间中，这块连续存储空间称为散列表或哈希表(Hash table)。那么关键字对应的记录存储位置我们称为散列地址。

#### 散列函数的构造方法

1. 直接定址法

​				直接取关键字的某个线性函数值为散列地址，散列函数为：
$$
H(key)=key或H(key)=a∗key+b
$$


2. 数字分析法

> 分析数字关键字在各位上的变化情况，取比较随机的位作为散列地址。

3. 平方取中法

> 平方取中法比较适合于不知道关键字的分布，而位数又不是很大的情况。
>
> 字面意思：平方之后取中间的数字作为散列地址

4. 除留余数法

​				散列函数：

> h（key） = key % 17
>
> > * p取素数（避免余数相关性）

5. 随机数法

$$
H(key)=random(key)
$$



#### 处理散列冲突

​				开放地址法（闭散列表）和链地址法（开散列表法）

1. 线性探测法

> 从冲突的的下一个位置起，依次寻找空的散列地址
>
> > * 存在堆积现象

​	公式：
$$
H 
i
​
 (key)=(f(key)+d 
i
​
 )%m (d 
i
​
 =1,2,3,...,m−1)
$$
​	此时： di = 0,1,2,3,...k

2. 二次（平方）探测法

​		公式
$$
H 
i
​
 (key)=(f(key)+d 
i
​
 )%m (d 
i
​
 =1,2,3,...,m−1)
$$
​	此时：di = 0,1,-1,2^2,-2^2,....k^2,-k^2

3. 拉链法

> 将所有关键字为同义词的记录存储在一个单链表中，我们称这种表为同义词子表，在散列表中只存储所有同义词子表的头指针。



## 排序

### 插入排序

```c
void InsertSort(int* arr, int n)
{
	for (int i = 0; i < n - 1; ++i)
	{
		//记录有序序列最后一个元素的下标
		int end = i;
		//待插入的元素
		int tem = arr[end + 1];
		//单趟排
		while (end >= 0)
		{
			//比插入的数大就向后移
			if (tem < arr[end])
			{
				arr[end + 1] = arr[end];
				end--;
			}
			//比插入的数小，跳出循环
			else
			{
				break;
			}
		}
		//tem放到比插入的数小的数的后面
		arr[end  + 1] = tem;
		//代码执行到此位置有两种情况:
		//1.待插入元素找到应插入位置（break跳出循环到此）
		//2.待插入元素比当前有序序列中的所有元素都小（while循环结束后到此）
	}
}

```

> 时间复杂度：	
>
> * 最坏（逆序）：O(n^2)
> * 最好（升序）：O(n)
>
> 空间复杂度：O(1)

### 希尔排序

```c
//希尔排序
void ShellSort(int* arr, int n)
{
	int gap = n;
	while (gap>1)
	{
		//每次对gap折半操作
		gap = gap / 2;
		//单趟排序
		for (int i = 0; i < n - gap; ++i)
		{
			int end = i;
			int tem = arr[end + gap];
			while (end >= 0)
			{
				if (tem < arr[end])
				{
					arr[end + gap] = arr[end];
					end -= gap;
				}
				else
				{
					break;
				}
			}
			arr[end + gap] = tem;
		}
	}
}

```

> 时间复杂度(平均)：O(N^1.3)
> 空间复杂度：O(1)

### 选择排序

```c
//选择排序
void swap(int* a, int* b)
{
	int tem = *a;
	*a = *b;
	*b = tem;
}
void SelectSort(int* arr, int n)
{
	//保存参与单趟排序的第一个数和最后一个数的下标
	int begin = 0, end = n - 1;
	while (begin < end)
	{
		//保存最大值的下标
		int maxi = begin;
		//保存最小值的下标
		int mini = begin;
		//找出最大值和最小值的下标
		for (int i = begin; i <= end; ++i)
		{
			if (arr[i] < arr[mini])
			{
				mini = i;
			}
			if (arr[i] > arr[maxi])
			{
				maxi = i;
			}
		}
		//最小值放在序列开头
		swap(&arr[mini], &arr[begin]);
		//防止最大的数在begin位置被换走
		if (begin == maxi)
		{
			maxi = mini;
		}
		//最大值放在序列结尾
		swap(&arr[maxi], &arr[end]);
		++begin;
		--end;
	}
}
c
```

> 时间复杂度：
>
> * 最坏情况：O(N^2)
> * 最好情况：O(N^2)
>
> 空间复杂度：O(1)

### 冒泡排序

```c
//冒泡排序
void BubbleSort(int* arr, int n)
{
	int end = n;
	while (end)
	{
		int flag = 0;
		for (int i = 1; i < end; ++i)
		{
			if (arr[i - 1] > arr[i])
			{
				int tem = arr[i];
				arr[i] = arr[i - 1];
				arr[i - 1] = tem;
				flag = 1;
			}
		}
		if (flag == 0)
		{
			break;
		}
		--end;
	}
}

```

> 时间复杂度：
>
> * 最坏情况：O(N^2)
> * 最好情况：O(N)
>
> 空间复杂度：O(1)

### 堆排序

> 堆的分类：
>
> * 大根堆：每个节点的值大于或等于左右孩子节点的值
> * 小根堆：每个节点的值小于或等于左右孩子节点的值

步骤：

1. 构造大根堆
2. 顶端与末尾值交换
3. 将剩下的n-1个数造次构造为大根堆，重复上述操作。



```c
//目标为升序
void HeapAdjust(int* arr, int start, int end)
{
	int tmp = arr[start];
	for (int i = 2 * start + 1; i <= end; i = i * 2 + 1)
	{
		if (i < end&& arr[i] < arr[i + 1])//有右孩子并且左孩子小于右孩子
		{
			i++;
		}//i一定是左右孩子的最大值
		if (arr[i] > tmp)
		{
			arr[start] = arr[i];
			start = i;
		}
		else
		{
			break;
		}
	}
	arr[start] = tmp;
}
void HeapSort(int* arr, int len)
{
	//第一次建立大根堆，从后往前依次调整
	for(int i=(len-1-1)/2;i>=0;i--)
	{
		HeapAdjust(arr, i, len - 1);
	}
	//每次将根和待排序的最后一次交换，然后在调整
	int tmp;
	for (int i = 0; i < len - 1; i++)
	{
		tmp = arr[0];
		arr[0] = arr[len - 1-i];
		arr[len - 1 - i] = tmp;
		HeapAdjust(arr, 0, len - 1-i- 1);
	}
}
int main()
{
	int arr[] = { 9,5,6,3,5,3,1,0,96,66 };
	HeapSort(arr, sizeof(arr) / sizeof(arr[0]));
	printf("排序后为:");
	for (int i = 0; i < sizeof(arr) / sizeof(arr[0]); i++)
	{
		printf("%d ", arr[i]);
	}
	return 0;
}
```



> 时间复杂度：时间复杂度为O(nlogn)
>
> 空间复杂度：O(1)



### 快速排序（挖坑法）

```c
int PartSort(int* arr, int left, int right)
{
    int key = arr[left];
    int hole = left;
    
    while (left < right)
    {
        while (left < right && arr[right] >= key)
        {
            right--;
        }
        arr[hole] = arr[right];
        hole = right;
        
        while (left < right && arr[left] <= key)
        {
            left++;
        }
        arr[hole] = arr[left];
        hole = left;
    }
    
    arr[hole] = key;
    return hole;
}

```

### 快速排序（库函数直接调用法）

函数原型

```c
void qsort(void* base/*数组名字*/,size_t num/*元素个数*/,size_t width/*单个元素所占字节*/,int(__cdecl*compare)(const void*,const void*)/*比较函数*/);
```

案例：

```c
#include<stdio.h>
#include<stdlib.h>
int cmp(const void *a,const void *b) {
	return *(int*)a-*(int*)b;
}
int main() {
	int n,i;
	scanf("%d",&n);
	int time[n];
	for(i=0; i<n; i++) {
		scanf("%d",&time[i]);
	}
	qsort(time,n,sizeof(int),cmp);
	for(i=0;i<n;i++){
		printf("%d ",time[i]);
	}
	return 0;
}
```



### 归并排序

```c
void Merge(int sourceArr[],int tempArr[], int startIndex, int midIndex, int endIndex){
    int i = startIndex, j=midIndex+1, k = startIndex;
    while(i!=midIndex+1 && j!=endIndex+1) {
        if(sourceArr[i] > sourceArr[j])
            tempArr[k++] = sourceArr[j++];
        else
            tempArr[k++] = sourceArr[i++];
    }
    while(i != midIndex+1)
        tempArr[k++] = sourceArr[i++];
    while(j != endIndex+1)
        tempArr[k++] = sourceArr[j++];
    for(i=startIndex; i<=endIndex; i++)
        sourceArr[i] = tempArr[i];
}
 
//内部使用递归
void MergeSort(int sourceArr[], int tempArr[], int startIndex, int endIndex) {
    int midIndex;
    if(startIndex < endIndex) {
        midIndex = startIndex + (endIndex-startIndex) / 2;//避免溢出int
        MergeSort(sourceArr, tempArr, startIndex, midIndex);
        MergeSort(sourceArr, tempArr, midIndex+1, endIndex);
        Merge(sourceArr, tempArr, startIndex, midIndex, endIndex);
    }
}
 
int main(int argc, char * argv[]) {
    int a[8] = {50, 10, 20, 30, 70, 40, 80, 60};
    int i, b[8];
    MergeSort(a, b, 0, 7);
    for(i=0; i<8; i++)
        printf("%d ", a[i]);
    printf("\n");
    return 0;
}
```







>  时间复杂度：O(nlogn)。
>
>  空间复杂度：O(N)，归并排序需要一个与原数组相同长度的数组做辅助来排序。

### 基数排序

```c
#include<math.h>
void testBS()
{
    inta[] = {2, 343, 342, 1, 123, 43, 4343, 433, 687, 654, 3};
    int *a_p = a;
    //计算数组长度
    intsize = sizeof(a) / sizeof(int);
    //基数排序
    bucketSort3(a_p, size);
    //打印排序后结果
    inti;
    for(i = 0; i < size; i++)
    {
        printf("%d\n", a[i]);
    }
    intt;
    scanf("%d", t);
}
//基数排序
void bucketSort3(int *p, intn)
{
    //获取数组中的最大数
    intmaxNum = findMaxNum(p, n);
    //获取最大数的位数，次数也是再分配的次数。
    intloopTimes = getLoopTimes(maxNum);
    inti;
    //对每一位进行桶分配
    for(i = 1; i <= loopTimes; i++)
    {
        sort2(p, n, i);
    }
}
//获取数字的位数
int getLoopTimes(intnum)
{
    intcount = 1;
    inttemp = num / 10;
    while(temp != 0)
    {
        count++;
        temp = temp / 10;
    }
    returncount;
}
//查询数组中的最大数
int findMaxNum(int *p, intn)
{
    inti;
    intmax = 0;
    for(i = 0; i < n; i++)
    {
        if(*(p + i) > max)
        {
            max = *(p + i);
        }
    }
    returnmax;
}
//将数字分配到各自的桶中，然后按照桶的顺序输出排序结果
void sort2(int *p, intn, intloop)
{
    //建立一组桶此处的20是预设的根据实际数情况修改
    intbuckets[10][20] = {};
    //求桶的index的除数
    //如798个位桶index=(798/1)%10=8
    //十位桶index=(798/10)%10=9
    //百位桶index=(798/100)%10=7
    //tempNum为上式中的1、10、100
    inttempNum = (int)pow(10, loop - 1);
    inti, j;
    for(i = 0; i < n; i++)
    {
        introw_index = (*(p + i) / tempNum) % 10;
        for(j = 0; j < 20; j++)
        {
            if(buckets[row_index][j] == NULL)
            {
                buckets[row_index][j] = *(p + i);
                break;
            }
        }
    }
    //将桶中的数，倒回到原有数组中
    intk = 0;
    for(i = 0; i < 10; i++)
    {
        for(j = 0; j < 20; j++)
        {
            if(buckets[i][j] != NULL)
            {
                *(p + k) = buckets[i][j];
                buckets[i][j] = NULL;
                k++;
            }
        }
    }
}
```

![ comparison ](/images/dataStructureSort.webp)

## 应试

### 概述作业

> 数据在计算机内存中的表示是指（） 。数据的存储结构
>
> 数据结构形式地定义为（K，R），其中K是（）的有限集合，R是K上的关系上的有限集合。数据元素
>
> 数据结构形式地定义为（D,S），其中D是数据元素的有限集合，S是D上的（）的有限集合。关系
>
> 一个广义表的表尾总是一个（ ）。广义表

### 线性表

> 对于线性表的各种操作，考虑：
>
> 1. length与MaxSize的关系
> 2. length是否为零
> 3. 输入的参数是否符合规则（大于或小于零，是否为空）

### 栈

> 对于栈的各种操作，考虑：
>
> 1. 栈空或栈满的情况
> 2. 输入的参数是否符合规则（大于或小于零，是否为空）

* 后缀表达式

> 从左往右开始扫描中缀表达式。
>         遇到数字直接加入后缀表达式
>         遇到运算符时：
>             a.若为’(‘则入栈
>             b.若为’)’，则依次把栈中的运算符加入后缀表达式，直到出现’(’，从栈中删除’)’。
>             c.若为除括号外的其他运算符，当其他优先级高于除’)'外的栈顶运算符时，直接入栈。
>         否则从栈顶开始，依次弹出比当前处理的运算符优先级高和优先级相等的运算符，
>         直到一个比它优先级低的或遇到了一个左括号为止。







### 队列

> 对于队列的各种操作，考虑：
>
> 1. 队列空和满的情况
> 2. 输入的参数是否符合规则（大于或小于零，是否为空）





### 图

> 用邻接矩阵表示有N个结点E条边的图时，深度优先遍历算法的时间复杂度是：C.O(N2)

### 栈与队列

>  下列关于栈的叙述中，错误的是：
>
> 1. **采用非递归方式重写递归程序时必须使用栈**
> 2. 函数调用时，系统要用栈保存必要的信息
> 3. **只要确定了入栈次序，即可确定出栈次序**
> 4. **栈是一种受限的线性表，允许在其两端进行操作**
>

>  最不适合用作栈的链表是（）。
>
> A.只有表头指针没有表尾指针的循环双链表
> 
> B.只有表尾指针没有表头指针的循环双链表
> 
>C.只有表尾指针没有表头指针的循环单链表
>  
>D.**只有表头指针没有表尾指针的循环单链表**

>  下列关于栈的叙述中，错误的是：
>
>  1. 采用非递归方式重写递归程序时必须使用栈
>  2. 函数调用时，系统要用栈保存必要的信息
>  3. 只要确定了入栈次序，即可确定出栈次序
>  4. 栈是一种受限的线性表，允许在其两端进行操作
>
>  A.仅 1
>
>  B.仅 1、2、3
>
>  **C.**仅 1、3、4
>
>  D.仅 2、3、4
>
>  循环队列的引入是为了（ ）。克服假溢出
>
>  
>
>  
>

### 树

> 树的后序遍历与其对应的二叉树的哪种遍历相同？中序
>
> 森林的中序遍历与对应二叉树的什么遍历序列相同？中序



### 排序

> 有组记录的排序码为{ 46，79，56，38，40，84 }，则利用堆排序的方法建立的初始堆为：
>
> 84，79，56，38，40，46

> 下面四种排序算法中，稳定的算法是：归并排序

> 快速排序下列排序算法中，时间复杂度不受数据初始状态影响，恒为O(NlogN)的是：
>
> 堆排序

> 对N个记录进行快速排序，在最坏的情况下，其时间复杂度是：
>
> O(N2)

> 下列关键码序列中，属于堆的是（ 　）。
>
> （15，30，22，93，52，71）