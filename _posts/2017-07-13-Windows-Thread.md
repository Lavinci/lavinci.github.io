---
layout: post
title: "Windows Thread"
excerpt: "线程管理"
date: 2017-07-13 18:10
tags: [windows]
---
# 线程管理


## 线程创建

```c++
#include <stdio.h>
#include <windows.h>


DWORD WINAPI ThreadProc(LPVOID lpParam)
{
	int i=0;
	
	while(i<20)
	{
		printf("I am form a thread,count= %d \n",i++);
	}
	return 0;
}


int main(int argc,char* argv[])
{
	HANDLE hThread;  
	DWORD dwThreadId;


	hThread = ::CreateThread(NULL,NULL,ThreadProc,NULL,0,&dwThreadId);
	printf("Id = %d \n",dwThreadId);

	::WaitForSingleObject(hThread,INFINITE);		//等待线程结束
	::CloseHandle(hThread);						//关闭句柄

	return 0;
}
```

## 优先级管理
  线程之间存在优先级问题,如下解决
```c++
#include <windows.h>
#include <stdio.h>

DWORD WINAPI ThreadIdle(LPVOID lpParam)
{
	int i=0;
	while(i++<10)
		printf("Idle Thread runing \n");

	return 0;
}

DWORD WINAPI ThreadNormal(LPVOID lpParam)
{
	int i=0;
	while(i++ < 10)
		printf("Normal Thread runing \n");

	return 0;
}

int main(int argc,char* argv[])
{
	DWORD dwThreadId;
	HANDLE h[2];

	h[0]=::CreateThread(NULL,0,ThreadIdle,NULL,CREATE_SUSPENDED,&dwThreadId);	//暂停态线程
	::SetThreadPriority(h[0],THREAD_PRIORITY_IDLE); 		//优先级(空闲)
	::ResumeThread(h[0]);

	h[1]=::CreateThread(NULL,0,ThreadNormal,NULL,0,&dwThreadId);	//默认的Normal

	::WaitForMultipleObjects(2,h,TRUE,INFINITE);		//等待多线程结束


	::CloseHandle(h[0]);
	::CloseHandle(h[1]);

	return 0;
}
```

## 线程同步

   多个线程同时访问一段数据,会产生奇怪的问题,由此引入临界区
   可把注释部分删除观察现象
   
```c++
#include <windows.h>
#include <stdio.h>
#include <process.h>

int g_nCount1=0;
int g_nCount2=0;

BOOL g_bContinue=TRUE;
CRITICAL_SECTION g_cs;

UINT _stdcall ThreadFunc(LPVOID);

int main(int argc,char* argv[])
{
	UINT uId;
	HANDLE h[2];

	::InitializeCriticalSection(&g_cs);	//临界区声明

	h[0]=(HANDLE)::_beginthreadex(NULL,0,ThreadFunc,NULL,0,&uId);
	h[1]=(HANDLE)::_beginthreadex(NULL,0,ThreadFunc,NULL,0,&uId);
	
	Sleep(1000);
	g_bContinue=FALSE;

	::WaitForMultipleObjects(2,h,TRUE,INFINITE);



	::CloseHandle(h[1]);
	::CloseHandle(h[2]);
	::DeleteCriticalSection(&g_cs);		删除临界区

	printf("gCount1= %d\n",g_nCount1);
	printf("gCount2= %d\n",g_nCount2);

	return 0;
}

UINT _stdcall ThreadFunc(LPVOID)
{
	while(g_bContinue)
	{
		::EnterCriticalSection(&g_cs);		//进入临界区
		g_nCount1++;
		g_nCount2++;
		::LeaveCriticalSection(&g_cs);		//离开临界区
	}

	return 0;
}
```