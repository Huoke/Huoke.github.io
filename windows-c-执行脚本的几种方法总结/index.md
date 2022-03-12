# Windows系统及编程 - 调用脚本的几种方式


<h1 align="center"> Windows C++ 执行脚本的几种方法总结 </h1>


## 一、WinExec
微软的官方给出的建议是：
{{< admonition tip "注意">}}
这个函数只是为了兼容16位Windows系统而提供的，开发中应用程序还是应该使用CreateProcess函数。
{{< /admonition >}}

但是我只是想运行一个vbe、bat脚本，来看看使用WinExec是否可行...

**语法**
```c++
UINT WinExec( 
    [in] LPCSTR lpCmdLine,
    [in] UINT uCmdShow
);
```
**参数**
- lpCmdLine：
    这是一个入参，它是要执行的应用程序的命令行，类似于"D:\notepad++.exe -help" 之类，是可以加应用自己的参数的。如果lpCmdLine中没有包含路径，系统将按照下面的顺序来查找这个可执行文件：
    
    1. 主调进程.exe文件所在的目录。
    2. 主调进程的当期目录
    3. Windows 系统目录，即GetSystemDirectory返回的System32子文件夹。
    4. Windows目录，即GetWindowsDirectory返回的目录
    5. PATH环境变量中列出的目录
-  uCmdShow：
    也是一个入参，这是一个显示选项，详细的显示设置查看<u>ShowWindows</u>函数

**返回值**

函数成功，返回值是一个大于31的数。如果失败，返回值是下面的参数：
| 返回错误码 | 错误码描述 |
| :----: | :----: |
| 0 | 系统资源不够或者内存不够 |
| ERROR_BAD_FORMAT | .exe文件是非法的 |
| ERROR_FILE_NOT_FOUND | 指定的文件没有找到 |
| ERROR_PATH_NOT_FOUND | 指定的路径没有找到 |

{{< admonition tip "提示">}}
**提示**
当启动的进程调用调用GetMessage函数或者达到超时时，WinExec函数返回。为了避免等待超时延时，请在调用WinExec启动的任何进程中尽快调用GetMessage函数。:(
{{< /admonition>}}

**安全方面评论**

WinExec在解析lpCmdLine参数时会把参数中第一个空格后的文件当成可执行文件名，这就带来一个隐患，可能会运行我们不期望的可执行文件，例如:
```C++
WinExec("C:\\Program Files\\testApp", ...);
```
由于WinExec对参数的解析策略，上面的例子中我们的程序可能会调用Program.exe程序。如果有恶意用户在系统上创建名为"Program.exe"的应用程序，WinExec调用任何使用Program File 目录的程序将运行这个恶意程序，这将是一场灾难。

为了避免这个问题，使用CreateProcess来替换WinExec。如果因为遗留或者必须使用WinExec，那就确保用双引号扩住，如下所示:
```C++
WinExec("\C:\"Program Files\\testApp.exe\" -L -S", ...);
```
## 二、CreateProcess的方式启动脚本

创建一个新进程和它的主线程。新进程是在被调用进程的安全上下文中运行。

如果调用进程正在模拟另一个用户，则新进程将使用调用进程的令牌，而不是模拟令牌。要在模拟令牌所代表的用户的安全上下文中运行新进程，请使用CreateProcessAsUser或者CreateProcessWithLogonW函数。:)

### 语法
```C++
BOOL CreateProcessA(
  [in, optional]      LPCSTR                lpApplicationName,
  [in, out, optional] LPSTR                 lpCommandLine,
  [in, optional]      LPSECURITY_ATTRIBUTES lpProcessAttributes,
  [in, optional]      LPSECURITY_ATTRIBUTES lpThreadAttributes,
  [in]                BOOL                  bInheritHandles,
  [in]                DWORD                 dwCreationFlags,
  [in, optional]      LPVOID                lpEnvironment,
  [in, optional]      LPCSTR                lpCurrentDirectory,
  [in]                LPSTARTUPINFOA        lpStartupInfo,
  [out]               LPPROCESS_INFORMATION lpProcessInformation
);
```
### 参数
- lpApplicationName：

- lpCommandLine：

    执行的命令行，字符串的最大长度是32767字符，包括了Unicode结尾的null字符。如果lpApplicationName是NULL，lpCommandLine的模块名部分仅限于MAX_PATH字符大小。

    {{< admonition tip "注意" >}}
    这个函数的Unicode版本 CreateProcessW，会修改这个参数的内容，因此，这个参数不能指向只读区(const 变量或者一个字符串字面值常量)。如果这个参数是一个常量，这个函数可能会出现访问违规。
    {{< /admonition >}}
    lpCommandLine参数可以为NULL，这种情况下，函数使用lpApplicationName参数作为执行命令行参数。

- lpProcessAttributes
    指向SECURITY_ATTRIBUTES结构体指针，这个结构体确定了返回的新进程对象句柄是否可以由子进程继承。如果lpProcessAttributes为NULL，则无法继承。

    SECURITY_ATTRIBUTES中的lpSecurityDescriptor成员为新进程指定安全描述符。如果lpProcessAttributes为NULL或者lpSecurityDescriptor为NULL，则进程将获得默认的安全描述符。进程的默认安全描述符中的ACL是来自于创建者的主令牌。Windows XP：进程的默认安全描述符中ACL来自于创建者的主令牌或者模拟令牌。使用 SP2 和 Windows Server 2003的Windows XP时，这种行为发了改变。
- lpThreadAttributes

- bInheritHandles
    如果此参数为TRUE，则调用进程中的每个可继承句柄都会被新进程继承。如果为FALSE，则调用进程中的句柄不会被继承。

    {{< admonition tip "注意" >}}
    继承的句柄与原始的句柄具有相同的值和访问权限。有关可继承句柄的更多讨论，参见[可继承句柄]()
    {{< /admonition>}}

    Terminal Service：不能夸会话继承句柄。此外，如果此参数为TRUE，则必须在与调用方相同的会话中创建进程。

    Protected Process Light(PPL) processes: 当PPL进程创建非PPL进程时，通用句柄继承被阻止，因为不允许从非PPL进程到PPL进程使用进程重复句柄。详细信息请参与[Process Security and Access Rights]()
- dwCreationFlags

    这个是影响新进程创建方式的标志，多个标志可以使用按位或的方式组合使用可用的标志在这里[创建标志](#anthor)

- lpEnvironment
- lpCurrentDirectory
- lpStartupInfo
- lpProcessInformation

{{< admonition tip "补充信息" >}}

<span id="anthor"> CreateProcess 的dwCreationFlags可用标志如下：</span>

{{< /admonition>}}


