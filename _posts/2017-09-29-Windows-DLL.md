---
layout: post
turtle: "动态链接库DLL"
excerpt: "本文意在讲解静态链接库与动态链接库的创建与使用"
data: 2017-09-29
tag: [Windows]
---
一、           介绍
本文意在讲解静态链接库与动态链接库的创建与使用，在此之前先来对二者的概念、区别及优缺点进行简要的阐述。其中大多内容参考相关网络资料，由于本人能力有限，不能确保完全准确无误，若有偏差之处请不吝指出。文中使用到的代码均在Visual Studio 2008中编译通过，如果您使用的IDE与本文不同，可根据实际情况进行相应项目创建与操作。希望本文内容对您有所帮助。

二、           概念定义
1.     分别编译与链接
大多数高级语言都支持分别编译（Compiling），程序员可以显式地把程序划分为独立的模块或文件，然后由编译器（Compiler）对每个独立部分分别进行编译。在编译之后，由链接器（Linker）把这些独立编译单元链接（Linking）到一起。链接方式分为两种：

(1)     静态链接方式：在程序开发中，将各种目标模块（.OBJ）文件、运行时库（.LIB）文件，以及经常是已编译的资源（.RES）文件链接在一起，以便创建Windows的.EXE文件。
(2)     动态链接方式：在程序运行时，Windows把一个模块中的函数调用链接到库模块中的实际函数上的过程。
2.     静态链接库与动态链接库
静态链接库（Static Library，简称LIB）与动态链接库（Dynamic Link Library，简称DLL）都是共享代码的方式。如果使用静态链接库（也称静态库），则无论你愿不愿意，.LIB文件中的指令都会被直接包含到最终生成的.EXE文件中。但是若使用.DLL文件，该.DLL文件中的代码不必被包含在最终的.EXE文件中，.EXE文件执行时可以“动态”地载入和卸载这个与.EXE文件独立的.DLL文件。

2.1.          动态链接方式
链接一个DLL有两种方式：

2.1.1           载入时动态链接（Load-Time Dynamic Linking）
使用载入时动态链接，调用模块可以像调用本模块中的函数一样直接使用导出函数名调用DLL中的函数。这需要在链接时将函数所在DLL的导入库链接到可执行文件中，导入库向系统提供了载入DLL时所需的信息及用于定位DLL函数的地址符号。（相当于注册，当作API函数来使用，其实API函数就存放在系统DLL当中。）

2.1.2           运行时动态链接（Run-Time Dynamic Linking）
使用运行时动态链接，运行时可以通过LoadLibrary或LoadLibraryEx函数载入DLL。DLL载入后，模块可以通过调用GetProcAddress获取DLL函数的入口地址，然后就可以通过返回的函数指针调用DLL中的函数了。如此即可避免导入库文件了。

2.2.          二者优点及不足
2.2.1           静态链接库的优点
(1)     代码装载速度快，执行速度略比动态链接库快；
(2)     只需保证在开发者的计算机中有正确的.LIB文件，在以二进制形式发布程序时不需考虑在用户的计算机上.LIB文件是否存在及版本问题，可避免DLL地狱等问题。
2.2.2           动态链接库的优点
(1)     更加节省内存并减少页面交换；
(2)     DLL文件与EXE文件独立，只要输出接口不变（即名称、参数、返回值类型和调用约定不变），更换DLL文件不会对EXE文件造成任何影响，因而极大地提高了可维护性和可扩展性；
(3)     不同编程语言编写的程序只要按照函数调用约定就可以调用同一个DLL函数。
2.2.3           不足之处
(1)     使用静态链接生成的可执行文件体积较大，包含相同的公共代码，造成浪费；
(2)     使用动态链接库的应用程序不是自完备的，它依赖的DLL模块也要存在，如果使用载入时动态链接，程序启动时发现DLL不存在，系统将终止程序并给出错误信息。而使用运行时动态链接，系统不会终止，但由于DLL中的导出函数不可用，程序会加载失败；
(3)     使用动态链接库可能造成DLL地狱。
2.3.          DLL 地狱
DLL 地狱（DLL Hell）是指因为系统文件被覆盖而让整个系统像是掉进了地狱。

简单地讲，DLL地狱是指当多个应用程序试图共享一个公用组件时，如某个DLL或某个组件对象模型（COM）类，所引发的一系列问题。

最典型的情况是，某个应用程序将要安装一个新版本的共享组件，而该组件与机器上的现有版本不向后兼容。虽然刚安装的应用程序运行正常，但原来依赖前一版本共享组件的应用程序也许已无法再工作。在某些情况下，问题的起因更加难以预料。比如，当用户浏览某些web站点时会同时下载某个Microsoft ActiveX控件。如果下载该控件，它将替换机器上原有的任何版本的控件。如果机器上的某个应用程序恰好使用该控件，则很可能也会停止工作。

在许多情况下，用户需要很长时间才会发现应用程序已停止工作。结果往往很难记起是何时的机器变化影响到了该应用程序。

这些问题的原因是应用程序不同组件的版本信息没有由系统记录或加强。而且，系统为某个应用程序所做的改变会影响机器上的所有应用程序—现在建立完全从变化中隔离出来的应用程序并不容易。

三、           静态链接库的创建与使用
在此通过一个实例来介绍静态库的创建与使用。在该实例中，我们将一个实现两整数相加求和的函数封装到静态库中供其他程序调用。

1.     创建
首先，使用Visual Studio 2008来创建一个带预编译头的静态库项目Static，该项目包含在名为Library的解决方案中。

1.1.          创建静态库项目
创建一个不带预编译头的静态链接库项目有以下几个步骤：

(1)     单击菜单命令“文件”-“新建”-“项目”，弹出“新建项目”对话框；
(2)     在弹出的“新建项目”对话框中，选择左边“类别”列表中选择“Visual C++”-“Win32”，在右边的“模版”中选择“Win32项目”；
(3)     在下方输入项目名称“Static”，并选择项目创建的位置，勾选“生成解决方案”，并输入解决方案名称“Library”，然后点击“确定”按钮；
(4)     点击两次“下一步”按钮，进入“应用程序设置”界面；
(5)     在“应用程序设置”界面中，选择“静态库”，并确保下方“附加选项”中的“预编译头”被勾选，然后点击“完成”按钮。
1.2.          编辑项目
经过上面的步骤，初步创建了一个带预编译头的静态库项目，接下来编辑该项目以达到我们的创建静态库的目的。

首先添加一个用于定义导出函数的源文件Static.cpp，编码实现两个整数相加的Add函数。源文件代码如下：
```c++
#include “StdAfx.h”     // 标准头文件



int Add(int a, int b)

{

    return a + b;

}
```


接着点击菜单命令，“工具”-“生成Static”。如果一切顺利的话，就会在解决方案的“Debug”目录中生成了名为“Static.lib”的静态链接库。

同时，需要给该静态链接库编写一个声明头文件Static.h，以便在链接时告知编译该链接库中的导出函数声明。Static.h中的代码很简单，只要声明一下Add函数就可以：

```c++
#ifndef __STATIC_H__              // 防止该头文件重复引用

#define __STATIC_H__



int Add(int a, int b);                   // 声明导出函数



#endif
```


接着点击菜单命令，“工具”-“生成Static”。如果一切顺利的话，就会在Library解决方案的Debug目录中生成了名为MyDLL.lib的静态链接库。



2.     使用
在Library解决方案下，再添加一个Win32控制台应用程序空项目UseLIB。程序主文件名为UseLIB.cpp，其中包含用于调用Add函数的程序入口函数main。将刚才创建的Static.lib及其声明头文件Static.h一同复制到UseLIB项目目录下。并在源文件UseLIB.cpp中使用预编译命令链接Static.lib（也可以在IDE的项目属性中设置链接器选项，或者只复制Static.h文件并设置UseLIB项目的“项目依赖项”为Static项目）。

源文件UseLIB.cpp中的代码如下：
```c++
#pragma comment(lib, “Static.lib”)         // 链接静态库Static.lib



#include <stdio.h>

#include “Static.h”      // 包含Static.lib的声明头文件，声明导出函数Add



int main(void)

{

       int a = 1, b = 2;



    printf(“%d+%d=%d/n”, a, b, Add(a, b));      // 调用Static.lib中的Add函数



    return 0;

}
```
接下来点击菜单命令，“工具”-“生成UseLIB”。如果顺利的话，就会在Library解决方案的Debug目录中生成了名为UseLIB.exe的可执行执文件，运行UseLIB.exe，将在控制台中输出结果：1+2=3

3.     注意
由于项目中创建的源文件为.CPP文件，即C++源文件，因此Visual C++按C++规范，并采用__cdecl调用约定对其进行编译。这样得到的导出函数就不能被C语言程序所调用。解决该问题的办法是在函数体名称前添加extern “C”修饰，告诉编译器，该函数按照C语言规范，并采用__cdecl调用约定进行编译。因此源文件Add.cpp中的代码可修改如下：
```c++
extern “C” int add(int a, int b)

{

    return a + b;

}
```
最后重新编译该静态链接库项目，导出函数Add就能够被C语言程序所调用了。

另一种不改变代码的方法是在“Static属性页”左边的列表中选择“配置属性”-“C/C++”-“高级”，然后在右边的“调用约定”选择“__cdecl (/Gd)”，“编译为”选择“编译为C++代码 (/TP)”。这种方法在不同的IDE上设置方法有所不同。

四、           动态链接库的创建与使用
在此同样通过一个实例来介绍动态链接库的创建与使用。在实例中，依然使用Add函数进行讲解，这样一方面可以沿用上面静态链接的有关内容，另一方面也可以了解动态链接库与静态链接库在创建和使用上的异同。

1.     创建
首先，在之前创建的Library解决方案中添加一个带预编译头的动态链接库项目，项目名称为Dynamic。使用不同IDE的朋友可以根据实际情况进行创建。

1.1.          创建动态链接库项目
创建一个带预编译头的动态链接库项目有以下几个步骤：

(1)     单击菜单命令“文件”-“新建”-“项目”，弹出“新建项目”对话框；
(2)     在弹出的“新建项目”对话框中，选择左边列表的“Visual C++”–“Win32”，在右边的项目模版中选择“Win32项目”；
(3)     在下方输入项目名称“Dynamic”，并选择项目创建的位置，然后点击“确定”按钮；
(4)     点击两次“下一步”按钮，进入“应用程序设置”界面；
(5)     在“应用程序设置”界面中，选择“DLL”，然后点击“完成”按钮。
1.2.          编辑项目
Dynamic项目自动生成的dllmain.cpp源文件含有一个名为DllMain的函数，该函数是DLL被链接时的入口函数，它由系统自动调用，在这里我们不用去理会它。

与前面创建静态态链接库类似的，首先添加一个用于定义导出函数的源文件Dynamic.cpp，编码实现两个整数相加的Add函数。源文件代码如下：

```c++
extern “C” __declspec(dllexport) int Add(int a, int b)    // 声明为DLL导出函数

{

    return a + b;

}
```


与前面静态链接库不同，在Add函数体名称前不只添加了extern “C”修饰，还多添加了一个__declspec(dllexport)修饰。__declspec(dllexport)修饰的作用是告诉编译器，这个函数将作为导出函数，并在输入库中生成该函数的地址符号等信息，这样其他程序就可以使用载入时动态链接方式来调用该函数。另外，extern “C”在封装DLL还有另一个作用，就是告诉编译器，在DLL中的导出函数不要使用函数名修饰规则，这样在采用运行时动态链接时就可以直接使用原函数名来调用导出函数了。关于函数调用方式和导出方式的详细说明在后面还将提出，现在先撇开这些烦人的问题。

接着点击菜单命令，“工具”-“生成Dynamic”。如果一切顺利的话，就会在Library解决方案的Debug目录中生成了名为Dynamic.dll的动态链接库和名为Dynamic.lib的导入库（与静态链接库不同，只存放DLL的导出表，不包含代码）。

最后需要给该Dynamic.dll的输入库Dynamic.lib编写一个声明头文件Dynamic.h，以便在以后链接时告知编译器该链接库中的具体的导入内容（一般包括代码和资源）。Dynamic.h中的代码很简单，只要声明一下Add函数就可以：
```c++
#ifndef __DYNAMIC_H__   // 防止该头文件重复引用

#define __DYNAMIC_H__



__declspec(dllexport) int Add(int a, int b);                    // 声明导出函数



#endif
```
2.     使用
在同Library解决方案中，添加一个名为UseDLL的Win32控制台应用程序空项目。程序主文件名为UseDLL.cpp，其中包含用于调用Add函数的程序入口函数main。一下使用两种动态链接方式来链接Dynamic.dll。

2.1.          载入时动态链接
载入时动态链接是一种轻松使用动态链接库的方法，它使得使用动态链接库如同使用静态链接库一样方便。将导入库Dynamic.lib及其声明头文件Dynamic.h一同复制到UseDLL项目目录下，并把Dynamic.dll复制到项目的Debug目录中。并在源文件UseDLL.cpp中使用预编译命令链接Dynamic.lib（也可以在IDE的项目属性中设置链接器选项，或者只复制Dynamic.h文件并设置UseDLL项目的“项目依赖项”为Dynamic项目）。

源文件UseDLL.cpp中的代码如下：
```c++
#pragma comment(lib, “Dynamic.lib”)    // 链接导入库Dynamic.lib



#include <stdio.h>

#include “Dynamic.h”  // 包含Dynamic.lib的声明头文件，提供导出函数Add的声明



int main(void)

{

       int a = 1, b = 2;



printf(“%d+%d=%d/n”, a, b, Add(a, b));      // 调用Dynamic.DLL中的Add函数

getchar();                                                       // 用于查看输出结果



    return 0;

}
```
几乎跟使用静态链接库一样。接下来点击菜单命令，“工具”-“生成UseDLL”。如果一切顺利的话，就会在Library解决方案的Debug目录中生成了名为UseDLL.exe的可执行文件，运行UseDLL.exe文件，将在控制台中输出结果：1+2=3

2.2.          运行时动态链接
运行时动态链接的代码相对麻烦些，需要使用到Windows的三个API函数，还要进行一些判断以防止不必要的麻烦。我们在UseDLL项目的基础上做些修改来实现运行时动态链接。这里只需要把Dynamic.dll复制到UseDLL项目的Debug目录中，因为不用在编译的时候链接导入库，只要在运行根据需要链接Dynamic.dll。下面先给出修改后的源文件Dynamic.cpp的代码：
```c++
#include <windows.h>       // 用于声明window API函数及宏等

#include <stdio.h>



typedef int (* FuncAdd)(int a, int b);       // 定义将要调用的导出函数Add的指针类型



int main(void)

{

       FuncAdd Add;       // 定义Add函数指针

       int a = 1, b = 2;

       HMODULE hDLL = LoadLibrary(TEXT("MyDLL.dll"));    // 载入DLL，并获取其句柄



       if (hDLL)        // MyDLL.dll载入成功

       {

              Add = (FuncAdd)GetProcAddress(hDLL, "Add");    // 获取导出函数Add指针

              if (Add)          // 正确获取Add函数指针

              {

                     printf("%d+%d=%d/n", a, b, Add(a, b));      // 调用导出函数Add

              }

              else               // 没有找到Add函数

              {

                     printf("Add Not Found!/n");

              }

       }

       else        // MyDLL.dll载入失败

       {

              printf("LoadLibrary Failed!/n");

       }



       getchar();

       FreeLibrary((TEXT("MyDLL.dll"));      // 释放DLL

       return 0;

}
```
看到了吧，调用方法比较繁琐。由于没有链接导入库，不能使用地址符号定位导出函数的入口地址，只能通过GetProcAdress来获取其在地址空间中的指针，再通过指针调用。但程序在运行之前，GetProcAdress无法判断指针的有效性。因此，为了防止Dynamic项目中不存在Add函数而使程序在运行时出错，有必要在调用Add之前判断其函数指针的有效性。

最后，点击菜单命令，“工具”-“重新生成UseDLL”。如果一切顺利的话，就会在Library解决方案的Debug目录中生成了名为UseDLL.exe的可执行文件，运行UseDLL.exe文件，将在控制台中输出结果：1+2=3

五、           补充阅读
1.     Visual Studio 2008中DLL项目只生成dll文件不生成lib文件的解决方案
在创建动态链接库项目时，如果在“应用程序设置”中只勾选“预编译头”，而没有勾选“空项目”或“导出符号”，那么在对该项目进行编译链接时将只会生成动态链接库dll文件，不生成导入库lib文件。此问题的解决办法如下：

(1)     右键单击该项目，在弹出的菜单中点击“添加”-“新建项”；
(2)     在弹出的“添加新项”对话框中，在左边“类别”列表中选择“Visual C++”-“代码”，在右边“模版”中选择“模块定义文件(.def)”；
(3)     在“名称”输入框中输入任意名称，这里使用该项目名称，如“MyDLL”，单击“确定”按钮；
(4)     重新生成该项目，即生成lib文件。
注意，此时在项目属性的配置列表“配置属性”-“链接器”-“输入”中的“模块定义文件”项目中将出现刚才创建的模块定义文件MyDLL.def。如果此前不是添加“新建项”，而是添加“现有项”，那么必须在此项目上填写该现有模块定义文件的文件名，否则将不会生成lib文件。

2.     动态链接库的应用举例
(1)     所有的Windows系统调用(Windows API函数)都是以动态链接库的形式提供的。我们在Windows目录下的system32文件夹中会看到kernel32.dll、user32.dll和gdi32.dll，windows的大多数API都包含在这些DLL中。kernel32.dll中的函数主要处理内存管理和进程调度；user32.dll中的函数主要控制用户界面；gdi32.dll中的函数则负责图形方面的操作。与这些动态库相对应的导入库分别为kernel32.lib、user32.lib和gdi32.lib。
(2)     软件的自动更新。Windows应用的开发者常常利用动态链接库来分发软件更新。他们生成一个动态库的新版本，然后用户可以下载，并用它替代当前的版本。当然，新、旧版本动态库的输出接口(即导出函数)必须一致。下一次用户运行应用程序时，应用将自动链接和加载新的动态库。
(3)     软件插件技术。许多Windows应用软件都支持插件扩展方式，如IE浏览器、Photoshop、Office等等。插件在本质上都是动态库。
(4)     可扩展的Web服务器。
(5)     每个Windows驱动程序在本质上都是动态链接库。


3.     __declspec(dllexport)与 .def文件
在 32 位编译器版本中，可以使用__declspec(dllexport)关键字从DLL导出数据、函数、类或类成员函数。__declspec(dllexport)将导出指令添加到对象文件(即obj文件)，若要导出函数，__declspec(dllexport)关键字必须出现在调用约定关键字的左边（如果指定了关键字）。例如：

__declspec(dllexport) void __cdecl Function1(void);

若要导出类中的所有公共数据成员和成员函数，关键字必须出现在类名的左边，如下所示：

class __declspec(dllexport) CExampleExport : public CObject

{ ... class definition ... };

生成DLL时，通常创建一个包含正在导出的函数原型和/或类的头文件，并将__declspec(dllexport)添加到头文件中的声明。

若要提高代码的可读性，请为__declspec(dllexport)定义一个宏并对正在导出的每个符号使用该宏：

#define Export __declspec(dllexport)

模块定义文件（.def）是包含一个或多个描述各种DLL属性的模块语句的文本文件。二者的目的都是将公共符号导入到应用程序中或从 DLL 导出函数。添加__declspec(dllexport)是为了提供不使用.def文件从 .EXE或.DLL导出函数的简单方法。如果不使用__declspec (dllimport)或__declspec(dllexport)导出DLL函数，则DLL需要.def文件。

并不是任何时候选择添加__declspec(dllexport)而放弃.def的方式都是好的。如果DLL是提供给VC++用户使用的，只需要把编译DLL时产生的.lib提供给用户，它可以很轻松地调用你的DLL。但是如果DLL是供VB、PB、Delphi用户使用的，那么会产生一个小麻烦。因为VC++对于__declspec(dllexport) 声明的函数会进行名称转换，比如函数：

__declspec(dllexport) int __stdcall IsWinNT()

会转换为IsWinNT@0，这样你在VB中必须这样声明：

Declare Function IsWinNT Lib "my.dll" Alias "IsWinNT@0" () As Long

@的后面的数由于参数类型不同而可能不同。这显然不太方便。所以如果要想避免这种转换，就要使用.def文件方式。

4.     函数调用约定（Calling Convention）
函数调用约定不仅决定了发生函数调用时函数参数的入栈顺序，还决定了是由调用者函数还是被调用函数负责清除栈中的参数，还原堆栈。函数调用约定有很多方式，除了常见的__cdecl，__fastcall和__stdcall之外，C++的编译器还支持thiscall方式，不少C/C++编译器还支持naked call方式。这么多函数调用约定常常令许多程序员很迷惑，到底它们是怎么回事，都是在什么情况下使用呢？下面就分别介绍这几种函数调用约定。

4.1.          各种调用约定
4.1.1           __cdecl
编译器的命令行参数是/Gd，__cdecl是C Declaration的缩写，是C和C++程序的缺省调用方式。所有参数从右到左依次入栈，这些参数由调用者清除，每一个调用它的函数都包含清空堆栈的代码，称为手动清栈。所以产生的可执行文件大小会比调用__stdcall函数的大。被调用函数无需要求调用者传递多少参数，调用者传递过多或者过少的参数，甚至完全不同的参数都不会产生编译阶段的错误。所有非C++成员函数和那些没有用__stdcall或__fastcall声明的函数都默认是__cdecl方式，它使用C函数调用方式。

4.1.2           __stdcall
__stdcall是Standard Call的缩写，是Pascal程序的缺省调用方式，通常用于Win32 API中，是WIN32 API的标准调用方式：所有参数从右到左依次入栈，如果是调用类成员的话，最后一个入栈的是this指针。这些堆栈中的参数由被调用的函数在返回后清除，使用的指令是 retn X，X表示参数占用的字节数，CPU在ret之后自动弹出X个字节的堆栈空间。称为自动清栈。函数在编译的时候就必须确定参数个数，并且调用者必须严格的控制参数的生成，不能多，不能少，否则返回后会出错。

4.1.3           __fastcall
__fastcall 是编译器指定的快速调用方式。由于大多数的函数参数个数很少，使用堆栈传递比较费时。因此__fastcall通常规定将前两个（或若干个）参数由寄存器传递，其余参数还是通过堆栈传递。不同编译器编译的程序规定的寄存器不同。返回方式和__stdcall相当。

4.1.4           __thiscall
__thiscall 是为了解决类成员调用中this指针传递而规定的。__thiscall要求把this指针放在特定寄存器中，该寄存器由编译器决定。VC使用ecx，Borland的C++编译器使用eax。返回方式和__stdcall相当。

4.1.5           naked call
     采用前面几种函数调用约定的函数，编译器会在必要的时候自动在函数开始添加保存ESI，EDI，EBX，EBP寄存器的代码，在退出函数时恢复这些寄存器的内容，使用naked call方式声明的函数不会添加这样的代码，这也就是为什么称其为naked的原因吧。naked call不是类型修饰符，故必须和_declspec共同使用。



4.2.          综述


__fastcall 和 __thiscall涉及的寄存器由编译器决定，因此不能用作跨编译器的接口。所以Windows上的COM对象接口都定义为__stdcall调用方式。

带有可变参数的函数必须且只能使用__cdecl方式，例如下面的函数：

int printf(char * fmtStr, ...);

int scanf(char * fmtStr, ...);

__stdcall和__cdecl这两个关键字看起来似乎很少和我们打交道，但是看了下面的定义（来自windef.h），你一定会觉得惊讶：

  #define CALLBACK    __stdcall

  #define WINAPI      __stdcall

  #define WINAPIV     __cdecl

  #define APIENTRY    WINAPI

  #define APIPRIVATE __stdcall

  #define PASCAL      __stdcall

  #define cdecl _cdecl

  #ifndef CDECL

  #define CDECL _cdecl

  #endif

  几乎我们写的每一个WINDOWS API函数都是__stdcall类型的，为什么？

  首先，我们谈一下两者之间的区别：

   WINDOWS的函数调用时需要用到栈。当函数调用完成后，栈需要清除，这里就是问题的关键，如何清除？

    如果我们的函数使用了__cdecl，那么栈的清除工作是由调用者，用COM的术语来讲就是客户来完成的。这样带来了一个棘手的问题，不同的编译器产生栈的方式不尽相同，那么调用者能否正常的完成清除工作呢？答案是不能。

    如果使用__stdcall，上面的问题就解决了，函数自己解决清除工作。所以，在跨开发平台的调用中，我们都使用__stdcall（虽然有时是以WINAPI的样子出现），如JNI。

    那么为什么还需要__cdecl呢？当我们遇到这样的函数如fprintf()它的参数是可变的，不定长的，被调用者事先无法知道参数的长度（如typedef int (*MYPROC)(LPTSTR, ...);），事后的清除工作也无法正常的进行，因此，这种情况我们只能使用__cdecl。

到这里我们有一个结论，如果你的程序中没有涉及可变参数，最好使用__stdcall关键字。

5.     函数名字修饰（Decorated Name）规则
函数的名字修饰（Decorated Name）就是编译器在编译期间创建的一个字符串，用来指明函数的定义或原型。LINK程序或其他工具有时需要指定函数的名字修饰来定位函数的正确位置。多数情况下程序员并不需要知道函数的名字修饰，LINK程序或其他工具会自动区分他们。当然，在某些情况下需要指定函数的名字修饰，例如在C++程序中，为了让LINK程序或其他工具能够匹配到正确的函数名字，就必须为重载函数和一些特殊的函数（如构造函数和析构函数）指定名字装饰。另一种需要指定函数的名字修饰的情况是在汇编程序中调用C或C++的函数。如果函数名字，调用约定，返回值类型或函数参数有任何改变，原来的名字修饰就不再有效，必须指定新的名字修饰。C和C++程序的函数在内部使用不同的名字修饰方式，下面将分别介绍这两种方式。

5.1.          C编译器的函数名修饰规则
对于__stdcall调用约定，编译器和链接器会在输出函数名前加上一个下划线前缀，函数名后面加上一个“@”符号和其参数的字节数，例如_functionname@number。__cdecl调用约定仅在输出函数名前加上一个下划线前缀，例如_functionname。__fastcall调用约定在输出函数名前加上一个“@”符号，后面也是一个“@”符号和其参数的字节数，例如@functionname@number。

5.2.          C++编译器的函数名修饰规则
 C++的函数名修饰规则有些复杂，但是信息更充分，通过分析修饰名不仅能够知道函数的调用方式，返回值类型，参数个数甚至参数类型。不管__cdecl，__fastcall还是__stdcall调用方式，函数修饰都是以一个“?”开始，后面紧跟函数的名字，再后面是参数表的开始标识和按照参数类型代号拼出的参数表。对于__stdcall方式，参数表的开始标识是“@@YG”，对于__cdecl方式则是“@@YA”，对于__fastcall方式则是“@@YI”。参数表的拼写代号如下所示：

代号       类型

X            void

D            char

E            unsigned char

F            short

H            int

I             unsigned int

J             long

K            unsigned long

M           float

N            double

_N          bool

O            long double

PA          指针前缀

AA          引用前缀

V类名@@     类



指针的方式有些特别，用PA表示指针，用PB表示const类型的指针，如果是引用，则在类型代号前加上AA。后面的代号表明指针类型，如果相同类型的指针连续出现，以“0”代替，一个“0”代表一次重复。如果相同类型的引用连续出现，则以“1”代替，每个“1”都代表一次重复。U表示结构类型，通常后跟结构体的类型名，用“@@”表示结构类型名的结束。函数的返回值不作特殊处理，它的描述方式和函数参数一样，紧跟着参数表的开始标志，也就是说，函数参数表的第一项实际上是表示函数的返回值类型。参数表后以“@Z”标识整个名字的结束，如果该函数无参数，则以“Z”标识结束。下面举三个个例子。

函数声明：int Function1(char *var1,unsigned long);

函数修饰：?Function1@@YGHPADK@Z

函数声明：void Function2();

函数修饰：?Function2@@YGXXZ

函数原型（Test为自定义类）：

void abc(int a, long b, char* c, char* d, bool &e, Test f, short g);

函数修饰名：?abc@@YAXHJPAD0AA_NVTest@@F@Z

对于C++的类成员函数（其调用方式是thiscall），函数的名字修饰与非成员的C++函数稍有不同，首先就是在函数名字和参数表之间插入以“@”字符引导的类名；其次是参数表的开始标识不同，公有（public）成员函数的标识是“@@QAE”，保护（protected）成员函数的标识是“@@IAE”，私有（private）成员函数的标识是“@@AAE”，如果函数声明使用了const关键字，则相应的标识应分别为“@@QBE”，“@@IBE”和“@@ABE”。如果参数类型是类实例的引用，则使用“AAV1”，对于const类型的引用，则使用“ABV1”。下面就以类CTest为例说明C++成员函数的名字修饰规则：

class CTest

{

......

private:

     void Function(int);

protected:

     void CopyInfo(const CTest &src);

public:

     long DrawText(HDC hdc, long pos, const TCHAR* text, RGBQUAD color, BYTE bUnder, bool bSet);

     long InsightClass(DWORD dwClass) const;

......

};



对于成员函数Function，其函数修饰名为“?Function@CTest@@AAEXH@Z”，字符串“@@AAE”表示这是一个私有函数。成员函数CopyInfo只有一个参数，是对类CTest的const引用参数，其函数修饰名为“?CopyInfo@CTest@@IAEXABV1@@Z”。DrawText是一个比较复杂的函数声明，不仅有字符串参数，还有结构体参数和HDC句柄参数，需要指出的是HDC实际上是一个HDC__结构类型的指针，这个参数的表示就是“PAUHDC__@@”，其完整的函数修饰名为“?DrawText@CTest@@QAEJPAUHDC__@@JPBDUtagRGBQUAD@@E_N@Z”。InsightClass是一个共有的const函数，它的成员函数标识是“@@QBE”，完整的修饰名就是“?InsightClass@CTest@@QBEJK@Z”。

无论是C函数名修饰方式还是C++函数名修饰方式均不改变输出函数名中的字符大小写，这和PASCAL调用约定不同，PASCAL约定输出的函数名无任何修饰且全部大写。

extern “C”使得其作用的函数采用C名字修饰方式进行编译。不要在C程序（源程序文件以.c作为后缀）中使用extern “C”，否则会出现错误。因为C编译器不认识extern “C”。

5.3.          查看函数的名字修饰
有两种方式可以检查你的程序中的函数的名字修饰：使用编译输出列表或使用Dumpbin工具。使用/FAc，/FAs或/FAcs命令行参数可以让编译器输出函数或变量名字列表。使用dumpbin.exe /SYMBOLS命令也可以获得obj文件或lib文件中的函数或变量名字列表。此外，还可以使用 undname.exe 将修饰名转换为未修饰形式。
