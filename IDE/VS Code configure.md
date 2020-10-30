<h1 align = center>Configuring C/C++ debugging</h1>
launch.json文件是用来给VS Code配置调试器的配置文件。
# VS Code

VS Code 生成一个launch.json文件将包含几乎所有所需的信息。 

首先，你需要在program字段中填写要调试的可执行文件的路径。必须对launch和attach都指定此选项。

文件包含两部分，一个是给launch配置调试，另一部分是给attach配置调试。

# 配置VS Code的 调试行为
通过设置和修改如下选项来控制调试时的行为：
- program(必要)
指定调试器能够launch和attach到可执行文件的全路径。调试器通过此路径来加载调试符号。
- symbolSearchPath
这个选项是告诉Visual Studio Windows Debugger 在什么地方查询符号(.pdb)文件。用分号来分割多个路径。eg:"C:\\Symbols;C:\\SymbolDir2"
	
	>pdb文件原名Program Data Base，即程序的基本数据，是VS编译链接时生成的文件。PDB文件主要存储了VS调试程序时所需要的基本信息，主要包括源文件名、变量名、函数名、FPO(帧指针)、对应的行号等。因为存储的是调试信息，所以一般情况下PDB文件是在Debug模式下才会生成。
	>

- requireExactSource
这个选项是告知 Visual Studio Windows Debugger 需要当前源码与pdb匹配。require exact source 也就是需要精确源码。

- additionalSOLibSearchPath
告诉GDB或者LLDB哪里可以搜索.so文件。多个路径使用分号分割。
eg: "/Users/user/dir1;Users/user/dir2"。

- externalConsole
控制台， 仅仅在启动(launch)调试器时使用。对于附加(attach)，这个参数不会改变调试器的表现的。
	- Windows：当设置为true时，它将会生成一个控制台。设置为false时， 将使用VS Code的综合终端。
	- Linux：设置为true时，将通知VS Code生成一个控制台。当设置为false时，将使用VS Code的综合终端。
	- macOS：设置为true时，将通过lldb-mi生成一个控制台。当设置为false时，输出信息将在VS Code的debug控制台输出。这是由于lldb-mi限制的原因，不支持综合终端。

- avoidWindowsConsoleRedirection
避免Windows控制台重定向选项。
为了支持在Windows平台上使用gdb的综合终端，此扩展将控制台重定向命令添加到调试器的参数中， 这样控制台的输入和输出将在综合终端中显示。
此选项设置为true将屏蔽此功能。

- logging
可选标志，确定可以将哪些消息记录到调试控制台。
	- execptions: 异常可选项，用于确定是否将异常消息记录到调试控制台，默认是true。
	- moduleLoad: 模块加载可选项， 用于确定是否应将模块加载事件记录到调试控制台。默认为true。
	- programOutput:程序输出可选项，用于确定程序输出是否应该记录到调试控制台。默认为true。
	- engineLogging: 引擎记录可选项，用于确定诊断引擎日志是否应该记录到调试控制台。默认为false。
	- trace：记叙可选项，用于确定诊断适配器命令记述是否应该记录到调试控制台。默认是false。
	- traceResponse：追踪响应可选项，用于确定诊断适配器命令和记叙响应是否应该记录到调试终端控制台。默认是false。
- visualizerFile
可视化工具文件。 在调试时.natvis文件将被使用。参见[创建自定义对象的视图](https://docs.microsoft.com/zh-cn/visualstudio/debugger/create-custom-views-of-native-objects?view=vs-2019)来查看怎么创建Natvis文件。
- showDisplayString
显示显示字符，指定可视化工具文件时，showDisplayString将启用显示字符串。启用此选项可能会导致调试期间性能降低。
看一个示例：
```json
{
  "name": "C++ Launch (Windows)",
  "type": "cppvsdbg",
  "request": "launch",
  "program": "C:\\app1\\Debug\app1.exe",
  "symbolSearchPath": "C:\\Symbols;C:\\SybolDir2",
  "externalConsole": true,
  "logging": {
    "moduleLoad": false,
    "trace": true
  },
  "visualizerFile": "${workspaceFolder}/my.natvis",
  "showDisplayString": true
}
```
# 配置目标应用
以下选项使您能够在目标应用程序启动时修改其状态。
- args
命令行参数的JSON数组，在程序启动时传给程序。例如: ["arg1", "arg2"]。如果是转义字符，那就需要双转义它们，例如, ["{\\\"arg1\\\": true}"]将发送{"arg1: true"}给你的应用程序。
- cwd
设置由调试器启动应该程序的工作目录。
- environment
环境变量可给你的项目添加环境变量。比如：[ { "name": "squid", "value": "clam" } ]。
示例：
```json
{
  "name": "C++ Launch",
  "type": "cppdbg",
  "request": "launch",
  "program": "${workspaceFolder}/a.out",
  "args": ["arg1", "arg2"],
  "cwd" : "${workspaceFolder}",
  "environment": [{ "name": "squid", "value": "clam" }]
}
```
# 自定义GDB / LLDB
以下选项可以更改GDB/LLDB的行为，我们先来了解一下GDB/LLDB有哪些行为。
>GDB/LLDB行为
>GDB和LLDB分别为linux和mac下的C语言调试器。 原理编译时输出带调试信息的程序,调试信息包含了指令地址、对应源代码及行号,指令完成后回调给调试器。

- MIMode
指示VS Code将要连接的调试器。必须设置为gdb或者lldb。这是在每个操作系统基础上预先配置的，可以根据需要进行更改。
- miDebuggerPath
调试器（如gdb）的路径。当只指定了可执行文件时，它将在操作系统的路径变量中搜索调试器（Linux和Windows上的GDB，OS X上的LLDB）。
- miDebugArgs
要传递给调试器的其他参数（如gdb）。
- stopAtEntry
如果设置为true，调试器应在目标的入口点停止（附加时忽略）。默认值为false。
- setupCommands
为了设置GDB或者LLDB而执行一些命令的json数组。比如："setupCommands": [ { "text": "target-run", "description": "run target", "ignoreFailures": false }]。
- customLaunchSetupCommands
自定义启动步骤命令。如果提供的话，它将用其他一些命令替换用于启动目标的默认命令。例如：可以是"-target-attach"目的是附加到目标进程。一个空的命令列表将使用空命令替换启动命令，如果调试器以命令行选项的形式提供启动选项，则此命令列表非常有用。
示例：
"customLaunchSetupCommands": [ { "text": "target-run", "description": "run target", "ignoreFailures": false }]。
- launchCompleteCommand
启动完成命令，在完全设置调试器后执行的命令，以便使目标进程运行。允许的值有："exec-run", "exec-continue", "None"。默认值是 "exec-run"。

查看一个示例：
```json
{
  "name": "C++ Launch",
  "type": "cppdbg",
  "request": "launch",
  "stopAtEntry": false,
  "customLaunchSetupCommands": [{"text": "target-run",
  "description": "run target",
  "ignoreFailures": false}
  ],
  "launchCompleteCommand": "exec-run",
  "linux": {
    "MIMode": "gdb",
    "miDebuggerPath": "/usr/bin/gdb"
  },
  "osx": {
    "MIMode": "lldb"
  },
  "windows": {
    "MIMode": "gdb",
    "miDebuggerPath": "C:\\MinGw\\bin\\gdb.exe"
  }
}
```
- symbolLoadInfo
	- loadAll：如果为true，则将加载所有lib的符号，否则将不加载solib符号。由ExceptionList修改。默认值为true。
	- exceptionList：文件名列表（允许使用通配符），用分号分隔";"。修改LoadAll的行为。如果LoadAll为true，则与列表中任何名称匹配的lib名不被加载符号。否则，只为匹配的lib加载符号。示例：“foo.so;bar.so"。
# 调试dump文件
C/C++的扩展能够使得在windows上调试转储文件、linux或者OS X的core文件。
- dumpPath
如果要调试Windows转储文件，请在启动配置中将其设置为要开始调试的转储文件的路径。
- coreDumpPath
要为指定程序调试的核心转储文件的完整路径。将其设置为核心转储文件的路径，以便在启动配置中开始调试。
>注意：MinGw不支持内核转储调试。

# 远程调试或者使用本地调试器来调试服务器
- miDebuggerServerAddress
要连接以进行远程调试的调试器服务器（例如，gdbserver）的网络地址（例如：本地主机：1234).
- debugServerPath
要启动调试的服务器的完全路径。
- debugServerArgs
调试远程服务器的参数。
- serverStarted
要在调试服务器输出中查找的服务器启动模式。
- serverLaunchTimeout
调试器等待debugServer启动的时间，默认单位为毫秒。
- pipeTransport
有关附加到远程进程的信息，例如在Docker容器中调试进程，请参阅[管道](https://code.visualstudio.com/docs/cpp/pipe-transport)传输设置文章。

# 其他属性
- processId
默认为${command：pickProcess}它将显示调试器可以附加到的可用进程的列表。我们建议您保留此默认值，但可以将属性显式设置为调试器要附加到的特定进程ID。
- request
指示配置节是要启动程序还是附加到已在运行的实例。
- targetArchitecture
不再需要此选项，因为将自动检测到目标体系结构。
- type

- sourceFileMap
# 环境变量定义文件
