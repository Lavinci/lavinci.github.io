---
layouts: post
title: "Windows Event"
excerpt: "关于事件"
date: 2017-07-14 15:15
tags: [windows]
---
# windows 中的事件机制

## 事件使用示例
```c++
#include <windows.h>
#include <stdio.h>
#include <process.h>

HANDLE g_hEvent;
DWORD WINAPI ChildFunc(LPVOID);

int main(int argc,char* argv[])
{
	HANDLE hChildThread;
	DWORD uId;

	g_hEvent = ::CreateEvent(NULL,FALSE,FALSE,NULL);//创建事件

	hChildThread = ::CreateThread(NULL,NULL,ChildFunc,NULL,0,&uId);//创建线程
	
	printf("Please input a char to tell the Child Thread to work :\n");
	getchar();

	::SetEvent(g_hEvent);	//触发事件
	::WaitForSingleObject(hChildThread,INFINITE);

	printf("All the work has been finished. \n");

	::CloseHandle(hChildThread);
	::CloseHandle(g_hEvent);

	return 0;
}

DWORD WINAPI ChildFunc(LPVOID)
{
	::WaitForSingleObject(g_hEvent,INFINITE);
	printf(" Child thread is working......\n");
	::Sleep(1000);
	return 0;
}
```
## 线程的局部存储TLS
  TLS是很方便的存储线程局部数据的系统,利用TLS机制可以为进程中的所有线程关联若干个数据,各个线程通过由TLS分配的全局索引来访问与自己相关联的数据,这样,每个线程都可以有线程局部的静态存储数据.
  
  ```c++
  #include <windows.h>
#include <stdio.h>
#include <process.h>

//利用TLS跟踪线程运行时间
DWORD g_tlsUsedTime; 	
void InitStartTime();
DWORD GetUsedTime();

UINT __stdcall ThreadFunc(LPVOID lpParam)
{
	int i;
	InitStartTime();//初始化开始时间
	i=10000*10000;//模拟工作时间
	while(i--){}
	printf("Thread Id:%5d      UsedTime: %d\n",::GetCurrentProcessId(),GetUsedTime());

	return 0;
}


int main(int argc,char* argv[])
{
	UINT uId;
	int i;
	HANDLE h[10];
	g_tlsUsedTime=::TlsAlloc();//申请索引

	//同时执行10个线程
	for (i=0;i<10;i++)
	{
		h[i]=(HANDLE)::_beginthreadex(NULL,0,ThreadFunc,NULL,0,&uId);//C++运行期里的线程创建函数
	}
	for(i=0;i<10;i++)
	{
		::WaitForSingleObject(h[i],INFINITE);
		::CloseHandle(h[i]);
	}
	//释放局部索引
	::TlsFree(g_tlsUsedTime);

	return 0;
}

void InitStartTime()
{
	//关联时间
	DWORD dwStart= ::GetTickCount();
	::TlsSetValue(g_tlsUsedTime,(LPVOID)dwStart);
}

DWORD GetUsedTime()
{
	DWORD dwElpsed=::GetTickCount();
	dwElpsed = dwElpsed-(DWORD)::TlsGetValue(g_tlsUsedTime);
	return dwElpsed;
}
  ```
  