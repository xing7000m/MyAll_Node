# 第一章


可在visual studio中进行中断, 调试 -> 窗口 -> 反汇编 来看反汇编代码  
___

在 `minwindef.h` 头文件中的定义

```
#define CALLBACK    __stdcall
#define WINAPI      __stdcall
#define WINAPIV     __cdecl
#define APIENTRY    WINAPI
#define APIPRIVATE  __stdcall
#define PASCAL      __stdcall
```
___
警告: "WinMain”的批注不一致: 此实例包含 无批注
```
 WinMain (
        _In_ HINSTANCE hInstance,
        _In_opt_ HINSTANCE hPrevInstance,
        _In_ LPSTR lpCmdLine,
        _In_ int nShowCmd
    );
```
这是因为在WinMain函数声明的每个参数的数据类型前都有一个参数说明符:`_In_` \ `_In_opt` 等  

`_In_` : 表明改参数是一个输入参数,在调用函数的时候为该参数设置一个值,函数约定只能读取该参数的值但是不可以修改  
`_Inout_` : 该参数是一个输入输出参数,在调用函数的时候为该参数设置一个值, 函数返回后会修改该参数的值  
`_Out_` : 该参数是一个输出参数,函数返回以后会在该参数中返回一个值  
`_Outprt_` : 该参数是一个输出参数,函数返回以后会在该参数中返回一个指针值  

还有 `_In_opt_` \ `_Inout_opt_` \ `_Out_opt` \ `_Outptr_opt_` : opt 表示可选择(optional), 表示可以不使用该参数, 也可直接设置为0 或者NULL  
___
H : Handle(句柄)  
INSTANCE : instance(实例)  

`HINSTANCE` 实例句柄  

可以看作: `HINSTANCE` 是一个32位或64位的无符号整数(存储的是内存地址)  
`HINSTANCE` 和 `HMODULE` 在底层上是一个东西, 可以相互强制转换.  
本质上都是 该模块被加载到进程内存空间后的基地址  

先说一下模块的概念。模块代表的是一个运行中的.exe或.dll 文件，表示这个文件中的所有  
代码和资源，磁盘上的文件不是模块，载入内存后运行时叫作模块；另外，一个应用程序调
用其他动态链接库中的API时，这些.dll文件也会被载入内存，这就产生了一些动态链接库
模块。为了区分地址空间中的不同模块，每个模块都有一个唯一的模块句柄来标识。模块句
柄实际上就是一个内存基地址，系统将.exe或.dll文件加载到地址空间的这个位置。   

在Win16时, 一个可执行文件运行后形成模块, 多次加载同一个可执行文件时, 这个模块时公用的. 为了区分多次加载的 "复制", 就把每个 "复制" 叫作实例, 每个实例均用不同的实例句柄(HINSTANCE)值来标识  
like : 打开一个txt文件. 文件代码相同,只不过数据不同. 代码部分就是模块. 数据就是实例.  

___

```
typedef char CHAR;

#define _Null_terminated_                 _SAL2_Source_(_Null_terminated_, (), _Null_terminated_impl_)

typedef _Null_terminated_ CHAR *NPSTR, *LPSTR, *PSTR;

```
`_Null_terminated_` : 表示以零结尾的字符串  
`LPSTR` : (Long Pointer To String)表示以零结尾的 `char` 类型字符串的指针  
Cmd 里输入 ``notepad.exe D:\111.txt``，`lpCmdLine` 拿到的仅仅是 ``"D:\111.txt"``。
___

WinMain的四个参数的含义:

``HINSTANCE hInstance`` : 表示应用程序的当前实例句柄, 作用就是: 唯一地标记了正在运行中的exe程序文件  

``HINSTANCE hPrevInstance ``: 表示应用程序的上一个实例的句柄,Win16历史遗留问题. 在Win32中始终为NULL(0)  

``LPSTR lpCmdLine`` : 指向应用程序命令行参数字符串的指针, 不包括可执行文件名.  
要获取整个命令行, 可以调用 `GetCommandLine` 函数  

int nCmdShow : 指定应用程序最初如何显示, 比如: 在任务栏上正常显示\最大化到全屏显示\最小化显示

___
“创建一个进程，类似直接创建了一个内存栈表。以这个表为表面，给程序提供了一套连续的内存来用；但实际上，程序真正使用的是这个栈表里存储的地址所指向的内存。”

| 我的原话理解                     | 对应专业术语                    | 实际含义与作用                                                                        |
| :------------------------------- | :------------------------------ | :------------------------------------------------------------------------------------ |
| “内存栈表”                       | **页表（Page Table）**          | 由操作系统建立并维护的数据结构。相当于一份“地址翻译目录”。                            |
| “表面连续的虚拟内存”             | **虚拟地址空间（VAS）**         | 程序运行时看到的“假象”。在 32 位系统下，每个程序都以为自己拥有连续、独立的 4GB 空间。 |
| “表里存储的地址（实际用的内存）” | **物理内存（Physical Memory）** | 真正存放代码和数据的地方（内存条）。实际上是碎片化的、不连续的。                      |

___

