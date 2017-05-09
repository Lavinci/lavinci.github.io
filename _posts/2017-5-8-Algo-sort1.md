---
layout: post
title: "Sort Advanced"
excerpt: "More Informations are comming!"
date: 2017-05-08 22:51:46 +08:00
tags: [Algo]
---

# 大波数据正在靠近

**上次插入排序作业的样例代码:**
```c++
int main()
{
	int a[]={10,9,8,7,6,5,4,3,2,1};
	int i;
	for(i=1;i<=10;i++)	//从第2个数据开始插入
	{
		int j=i-1;	
		int tmp=a[i];	//记录要插入的数据
		while(j>=0 && a[j]>tmp)//想想这里为什么要用tmp而不能直接用a[i]
		{
			a[j+1]=a[j];//从后向前，找到比其小的数的位置
			--j;	//向后挪动
		}
		If(j != i-1)//存在比其小的数
		a[j+1]=tmp; 
		
	}
	for(i=0;i<10;i++)
	cout<<a[i]<<" ";	
	return 0;
}
```
请认真理解这段代码,这是接下来的基础  

插入排序的算法复杂度为O(n2)，但如果序列为正序可提高到O(n)，而且直接插入排序算法比较简单，希尔排序利用这两点得到了一种改进后的插入排序.  

希尔排序：将无序数组分割为若干个子序列，子序列不是逐段分割的，而是相隔特定的增量的子序列，对各个子序列进行插入排序；然后再选择一个更小的增量，再将数组分割为多个子序列进行排序......最后选择增量为1，即使用直接插入排序，使最终数组成为有序。

增量的选择：在每趟的排序过程都有一个增量，至少满足一个规则 增量关系 d[1] > d[2] > d[3] >..> d[t] = 1 (t趟排序)；根据增量序列的选取其时间复杂度也会有变化，这个不少论文进行了研究，在此处就不再深究；本文采用首选增量为n/2,以此递推，每次增量为原先的1/2，直到增量为1；
下图详细讲解了一次希尔排序的过程：

![pc1](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/wechat/AlgoShellSort1.png)
![pc2](https://raw.githubusercontent.com/Lavinci/PicOnNet/master/article/wechat/AlgoShellSort2.png)

## 一:

###分析:

平均时间复杂度：希尔排序的时间复杂度和其增量序列有关系，这涉及到数学上尚未解决的难题；不过在某些序列中复杂度可以为O(n1.3);
空间复杂度：O(1)  
稳定性：不稳定
```c++
#include <iostream>
using namespace std;
void ShellInsert(int *p,int d,int n)
{
	for(int i=d;i<10;i++)//从第d个数据开始插入

	{
		int j=i-d;
		int tmp=p[i];	//记录要插入的数据

		while(j>=0 && p[j]>tmp)
		{
			p[j+d]=p[j];//从后向前，找到比其小的数的位置
			j-=d;
		}
if(j != i-d) //存在比其小的数

		p[j+d]=tmp;
		
	}
}
void ShellSort(int* p,int n)
{
	int d=n/2;
	while(d>=1)
	{
		insert(p,d,n);
		d /= 2;	
	}
}


int main()
{
	int a[]={10,9,8,7,6,5,4,3,2,1};
	shellsort(a,10);
	for(int i=0;i<12;i++)
	cout<<a[i]<<" ";	
	return 0;
}
```
 二:
 
合并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。

首先考虑如何将将二个有序数列合并。这个非常简单，只要从比较二个数列的第一个数，谁小就先取谁，取了后就在对应数列中删除这个数。然后再进行比较，如果有数列为空，那直接将另一个数列的数据依次取出即可。

```c++
//将有序数组a[]和b[]合并到c[]中  
void MemeryArray(int a[], int n, int b[], int m, int c[])  
{  
    int i, j, k;  
  
    i = j = k = 0;  
    while (i < n && j < m)  
    {  
        if (a[i] < b[j])  
            c[k++] = a[i++];  
        else  
            c[k++] = b[j++];   
    }  
  
    while (i < n)  
        c[k++] = a[i++];  
  
    while (j < m)  
        c[k++] = b[j++];  
}  
```

可以看出合并有序数列的效率是比较高的，可以达到O(n)。

解决了上面的合并有序数列问题，再来看归并排序，其的基本思路就是将数组分成二组A，B，如果这二组组内的数据都是有序的，那么就可以很方便的将这二组数据进行排序。如何让这二组组内数据有序了？

可以将A，B组各自再分成二组。依次类推，当分出来的小组只有一个数据时，可以认为这个小组组内已经达到了有序，然后再合并相邻的二个小组就可以了。这样通过先递归的分解数列，再合并数列就完成了归并排序。

根据这个思想我们改动刚才归并的代码
将有二个有序数列a[first...mid]和a[mid...last]合并。  

```c++
void mergearray(int a[], int first, int mid, int last, int temp[])  
{  
    int i = first, j = mid + 1;  
    int m = mid,   n = last;  
    int k = 0;  
    while (i <= m && j <= n)  
    {  
        if (a[i] <= a[j])  
            temp[k++] = a[i++];  
        else  
            temp[k++] = a[j++];  
    }  
      
    while (i <= m)  
        temp[k++] = a[i++];  
      
    while (j <= n)  
        temp[k++] = a[j++];  
      
    for (i = 0; i < k; i++)  
        a[first + i] = temp[i];  
}  

void mergesort(int a[], int first, int last, int temp[])  
{  
If (first > last)	return ;
    if (first < last)  
    {  
        int mid = (first + last) / 2;  
        mergesort(a, first, mid, temp);    //左边有序  
        mergesort(a, mid + 1, last, temp); //右边有序  
        mergearray(a, first, mid, last, temp); //再将二个有序数列合并  
    }  
} 
```
归并排序的效率是比较高的，设数列长为N，将数列分开成小数列一共要logN步，每步都是一个合并有序数列的过程，时间复杂度可以记为O(N)，故一共为O(N*logN)。因为归并排序每次都是在相邻的数据中进行操作，所以归并排序在O(N*logN)的几种排序方法（快速排序，归并排序，希尔排序，堆排序）也是效率比较高的.
留个作业,请大家自己比较一下快速排序跟合并排序到底有什么本质上的不同哦
排序我们就基本讲完了,堆排序我们到学完图和树之后再讲
下次我们讲数据结构,队列,栈,链表,可以先自行预习哦
