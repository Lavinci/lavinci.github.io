---
layout: post
title: Date Struct
excerpt: "Just Learning!"
date: 2017-05-09 22:07:03 +08:00
tags: [Algo]
---
#栈 队列 链表 向量

##栈

栈是一种FILO(first input last output,先进后出)的结构模型,图像上长这个样:
![stack](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/wechat/DataStruct1.png)

我们用一个int top 指向最上面的元素即可
### 例:
```c++
初始化
Int a[100];
Top=0;
```
### 入栈:
```c++
a[top++]=data;	//此处data表示输入的数据一般值;将值读入后,top+1
Data = a[top--];	//岀栈:
将值岀栈后top-1
```

将这些封装到类中
```c++
Class Stack
{
Public:
...
Void Push(int data);//入栈函数,请根据上面引例实现
Int Pop();//岀栈函数,请根据上面引例实现
...
Private:
Int a[100];
};
```

>POP,PUSH,这个名字是程序界默认的写法,有个笑话说看一个人是不是程序员就去问他push的反义词,回答pull的是普通人,回答pop的是程序员^_^

### 队列:
队列是一种FIFO的模型,只能从尾部加入,从头部取出,想象一下你去排队,是不是先排先到?图像长这样
![line](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/wechat/DataStruct2.png)  

我们用head指向头部,用tail指向尾部即可
例:

```c++
初始化:
Int a[100];
Head=tail=0;//我们认为head=tail队列即为空
入队;
a[tail++]=data;
出队:
Head++;
类:
Class Line
{
Public:
...
Void Push(int data);
Int POP();
...
Private:
Int a[100];
};
```
队列多用来处理数据的缓存,例如服务器最多处理10个数据,然而一次传入了100个数据,那最好就是用队列来,减缓数据输入速度了0.0

### 链表:

链表是一种在物理地址上不连续,逻辑上连续的数据结构,像上面两个例子我为了简单就直接用了一个数组,但当数据大到上M时,数组这种连续的空间很难找到,那最好就是向系统申请地址来处理链表的一般结构表示如下:

![nodeline](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/wechat/DataStruct3.png)

我们定义一个结构体来实现这个模型
```c++
Struct Node
{
Int data;
Node *next;
};	
```

#### 每次申请一个Node并赋相应的值即可
```c++
Node* alloca_node(int value)  
{  
    Node* pLinkNode = NULL;  
    pLinkNode = new Node;    
    pLinkNode->data = value;  
    pLinkNode->next = NULL;  
    return pLinkNode;  
}  
```
#### 删除链节点
```c++
void delete_node(Node** pNode)  
{  
    Node** pNext;  
    if(NULL == pNode || NULL == *pNode)  
        return ;  
          
    pNext = &(*pNode)->next;  
    free(*pNode);  
    delete_node(pNext);   
}  
```

#### 链表插入数据
```c++
add_data(Node** pNode, Node* pDataNode)  
{  
    if(NULL == *pNode){  
        *pNode = pDataNode;  
        return TRUE;  
    }  
      
    return _add_data(&(*pNode)->next, pDataNode);  
}  
```
#### 删除数据
 ```c++
STATUS _delete_data(LINK_NODE** pNode, int value)  
{  
    LINK_NODE* pLinkNode;  
    if(NULL == (*pNode)->next)  
        return FALSE;  
      
    pLinkNode = (*pNode)->next;  
    if(value == pLinkNode->data){  
        (*pNode)->next = pLinkNode->next;  
        free(pLinkNode);  
        return TRUE;  
    }else{  
        return _delete_data(&(*pNode)->next, value);  
    }  
}  
```
#### 查找数据
```c++
LINK_NODE* find_data(const LINK_NODE* pLinkNode, int value)  
{  
    if(NULL == pLinkNode)  
        return NULL;  
      
    if(value == pLinkNode->data)  
        return (LINK_NODE*)pLinkNode;  
      
    return find_data(pLinkNode->next, value);  
}  
```
#### 打印数据
```c++
void print_node(const LINK_NODE* pLinkNode)  
{  
    if(pLinkNode){  
        printf("%d\n", pLinkNode->data);  
        print_node(pLinkNode->next);  
    }  
}  
```
#### 统计数据
```c++
int count_node(const LINK_NODE* pLinkNode)  
{  
    if(NULL == pLinkNode)  
        return 0;  
          
    return 1 + count_node(pLinkNode->next);  
}  
```
