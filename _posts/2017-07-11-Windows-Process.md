---
layout: post
title: "Windows 进程管理"
except: "系统编程"
date: 2017-07-11 16:41
tags: [windows]
---
# Win32程序控制

## 进程和线程

  简而言之,一个程序至少有一个进程,一个进程至少有一个线程. 
线程的划分尺度小于进程，使得多线程程序的并发性高。
另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.
线程是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.
一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行.

进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

## 应用程序的启动过程

  应用程序必须有一个入口函数,它在程序开始运行时被调用.如果创建的是控制台应用程序,次入口函数将会是
  	int main(int argc,char* argv[])
  操作系统事实上并不是真的调用main函数,而是去调用C/C++运行期启动函数,此函数会初始化C/C++运行期库.因此,在程序中可以调用malloc和free之类的函数.他也保证了用户的代码执行之前所有的全局或静态的C++对象能够被正确的创建,即执行只写对象构造函数中的代码.
在Win32应用程序的启动过程就是进程的创建过程，操作系统是通过调用CreateProcess函数来创建新的进程的。系统接着会为新进程创建一个主线程，这个主线程通过执行C/C++运行期启动代码开始运行，C/C++运行期启动代码又会调用main函数。系统在创建新的进程时会为新进程指定一个STARTUPINFO类型的变量，这个结构包含了父进程传递给子进程的一些显示信息。对图形界面应用程序来说，这些信息将影响新的进程中主线程的主窗口的显示；对控制台应用程序来说，如果有一个新的控制台窗口被创建的话，这些信息将影响这个控制台窗口。
STARTUPINFO结构定义如下：
```c++
typedef struct
{
       DWORD cb;       //结构的长度，总是应该被设为sizeof(STARTUPINFO)
       LPSTR lpReserved;     //保留字段
       LPSTR lpDesktop;      //指定桌面名称
       LPSTR lpTitle;            //控制台应用程序使用，指定控制台窗口标题
       DWORD dwX;   //指定新创建窗口的位置坐标和大小信息
       DWORD dwY;
       DWORD dwXSize;
       DWORD dwYSize;
       DWORD dwXCountChars;     //控制台程序使用，指定控制台窗口的行数
       DWORD dwYCountChars;
       DWORD dwFillAttributr;        //控制台程序使用，指定控制台窗口的背景色
       DWORD dwFlags;    //标志它的值决定了STARTUPINFO结构中哪些成员的值是有效的
       WORD wShowWindow;        //窗口的显示方式
       WORD cbReserved2;
       LPBYTE  lpReserved2;
       HANDLE hStdInput;         //控制台程序使用，几个标准句柄
       HANDLE hStdOutput;
       HANDLE hStdError;  
}STARTUPINFO, *LPSTARTUPINFO;
 ```
一个进程可以调用GetStartupInfo函数来取得父进程创建自己时使用的STARTUPINFO结果。事实上，Windows系统就是通过调用这个函数来取得当前进程的创建信息，以便对新进程中主窗口的属性设置默认值。函数定义如下：
```c++
VOID GetStartupInfo(LPSTARTUPINFO lpStartupInfo);     //取得当前进程被创建时指定的STARTUPINFO
```

定义一个STARTUPINFO结构的对象后，总要在使用此对象之前将对象的cb成员初始为
STARTUPINFO结构的大小：

STARTUPINFO si = {sizeof(si)};            //将cb成员初始化为asizeof(si)，其他成员初始化为0
::GetStartupInfo(&si);

初始化cb成员是必须的，因为随着Windows版本的改变，API函数支持的结构体的成员有可能增加，
但又要兼容以前版本，所以Windows要通过结构体的大小来确定其成员的数目。

## 进程的创建

CreateProcess函数创建一个新的进程和该进程的主线程。新的进程会在父进程的安全上下文中运行指定的可执行文件：
```c++
CreateProcess(
       LPCSTR lpApplicationName,         //可执行文件的名称
       LPSTR lpCommandLine,          //指定了要传递给执行模块的参数
       LPSECURITY_ATTRIBUTES lpProcessAttributes,        //进程安全性，值为NULL的话表示使用默认的安全属性
       LPSECURITY_ATTRIBUTES lpThreadAttributes, //线程安全性，值为NULL的话表示使用默认的安全属性
       BOOL bInheritHandles,           //指定了当前线程中的可继承句柄是否可被新进程继承
       DWORD dwCreationFlags,     //指定了新进程的优先级以及其他创建标识
       LPVOID lpEnvironment,   //指定新进程使用的环境变量
       LPCSTR lpCurrentDirectory,    //新进程使用的当前目录
       LPSTARTUPINFO lpStartupInfo,    //指定新进程中主窗口的位置、大小和标准句柄等
       LPPROCESS_INFORMATION lpProcessInformation       //【out】返回新建进程的标志信息，如ID号、句柄等
       );
 ```
 
## 遍历进程
  使用ToolHelp函数可以获取当前正在运行的一系列进程。首先使用CreateToolhelp32Snapshot函数给当前系统内执行的进程拍快照，也就是获得一个进程列表，这个列表中记录着进程的ID、进程对应的可执行文件的名称和创建该进程的进程ID等数据。接着使用Process32First函数和Process32Next函数遍历快照中记录的列表

```c++
HANDLE WINAPI CreateToolhelp32Snapshot(
	DWORD dwFlags,	//用来指定返回对象,这里我们用TH32CS_SNAPPROCESS
	DWORD th32ProcessID	//指定进程的ID
);

BOOL bMore=::Process32First(hProcessSnap,&pe32);
while(bMore)
{
	//这里处理得到的信息
	bMore=::Process32Next(hProcessSnap,&pe32);
}

typedef struct
{
       DWORD dwSize,       //结构的长度，必须预先设置
       DWORD cntUsage,    //进程的引用计数
       DWORD th32ProcessID,  //进程ID
       DWORD th32DefaultHeapID,       //进程默认堆的ID
       DWORD th32ModuleID, //进程模块的ID
       DWORD cntThreads, //进程创建的线程数
       DWORD th32ParentProcessID,      //进程的父进程ID
       LONG pcPriClassBase,      //进程创建的线程的基本优先级
       DWORD dwFlags,     //内部使用
       CHAR szExeFile[MAX_PATH]       //进程对应的可执行文件名
}PROCESSENTRY32;

```
## 终止进程

终止进程也就是结束程序的执行，从内存中卸载它，进程终止的原因有4种：
1）主线程的入口函数返回；
2）进程中一个线程调用了ExitProcess函数；
3）此进程中的所有线程都结束了；
4）其他进程中的一个线程调用了TerminateProcess函数。
 
要结束进程一般让主线程的入口函数（main函数）返回。当用户的程序入口函数返回时，启动函数会调用C/C++运行期退出函数exit，并将用户的返回值传递给它。Exit函数会销毁所有全局的或静态的C++对象，然后调用系统函数ExitProcess此时操作系统终止应用程序：
void ExitProcess(UINT uExitCode);      //参数uExitCode为此程序的退出代码
我们也可以在程序的任何地方调用ExitProcess强制当前程序立即结束。对操作系统，这是很正常的；但对C/C++应用程序来说，应该避免直接调用这个函数，因为这会使C/C++运行期库得不到通知，而没有机会去调用全局或静态的C++对象的析构函数。
 
ExitProcess函数只能用来结束当前进程，不能用于结束其他进程。如果要结束其他进程的执行，可以使用TerminateProcess函数：
BOOL TerminateProcess(
       HANDLE hProcess,     //要结束的进程(目标进程)的句柄
       UINT uExitCode //指定目标进程的退出代码，
 //可以使用GetExitCodeProcess取得一个进程的退出代码
);
在使用TerminateProcess函数之前，我们首先需要取得目标进程的句柄，可以使用OpenProcess函数来取得这个进程的访问权限：
```c++
HANDLE OpenProcess(
       DWORD dwDesiredAccess,     //想得到的访问权限(PROCESS_ALL_ACCESS等)
       BOOL hInheritHandle,     //指定返回的句柄是否可继承
       DWORD dwProcessId             //指定要打开的进程的ID号
);
```
一般使用下面代码来终止一个进程：
```c++
BOOL TerminateProcessFromId(DWORD dwId)
{
       BOOL bRet = FALSE;
       //打开目标进程，取得进程句柄
       HANDLE hProcess = ::OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwId);
       if(hProcess != NULL)
       {
              bRet = ::TerminateProcess(hProcess, 0);
       }
       CloseHandle(hProcess);
       return bRet;
}
```
一旦进程终止，会有如下事件发生：
1）所有被这个进程创建或打开的对象句柄就会关闭；
2）此进程内的所有线程将会终止执行；
3）进程内核对象变成受信状态，所有等待在此对象上的线程开始运行，即WaitForSingleObject函数返回；
4）系统将进程对象中退出代码的值由STILL_ACTIVE改为指定的退出码。


## 实例:关闭QQ

```c++
#include <windows.h>
#include <tlhelp32.h>
#include <iostream>
#include <string.h>

using namespace std;

int main(int argc,char* argv[])
{
	//获取QQ的PID
	int Id;
	HANDLE hIdProcess=::CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS,0);
	PROCESSENTRY32 pe32;
	pe32.dwSize=sizeof(pe32);

	BOOL bMore = ::Process32First(hIdProcess,&pe32);
	while(bMore)
	{
		if(!strcmp(pe32.szExeFile,"QQ.exe"))
		{
			Id=pe32.th32ProcessID;
			break;
		}
		bMore=::Process32Next(hIdProcess,&pe32);
	}

	//取得句柄权限
	HANDLE hTargetProcess = ::OpenProcess(PROCESS_ALL_ACCESS,FALSE,Id);		

	BOOL bRet=FALSE;

	if(hTargetProcess!=NULL)
	{
		bRet=::TerminateProcess(hTargetProcess,0);
	}
	
	cout<<bRet<<endl;

	::CloseHandle(hTargetProcess);
	::CloseHandle(hIdProcess);
	//关闭句柄
	
	return 0;
}
```
## 结语
   随便几行代码便能结束任意程序(不包含有自保护的),真的好吗?