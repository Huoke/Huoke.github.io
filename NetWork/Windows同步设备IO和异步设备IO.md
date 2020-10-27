<h1>同步设备IO和异步设备IO</h1>

## 4 异步设备IO基础

假设一个线程向设备发出一个I/O请求, 这个I/O请求被传给设备驱动程序，设备驱动程序负责完成实际的I/O操作。当驱动程序在等待设备响应的时候，应用程序的线程则可以继续处理其他任务。这就是异步I/O。

当某一时刻驱动程序完成了对队列中I/O请求的处理，此时驱动程序通过内核来通知应用程序数据已发完、数据已收到或者发生了错误。

首先我们一起研究一下，如何把异步I/O请求加入队列。设计高性能、可伸缩的应用程序的本质就是怎么实现把异步I/O加入请求队列。
>> CreateFile此函数的详细简绍参见微软官方文档或者上面1、2标题
```C++
HANDLE CreateFile(
  LPCTSTR lpFileName,
  DWORD dwDesiredAccess,
  DWORD dwShareMode,
  LPSECURITY_ATTRIBUTES lpSecurityAttributes,
  DWORD dwCreationDisposition,
  DWORD dwFlagsAndAttributes,
  HANDLE hTemplateFile
);
```
CreateFile这个函数创建、打开或者truncates 一个文件，COM port，设备，服务或者终端，它返回一个访问此对象的句柄。

为了异步的方式访问设备，在创建设备时，CreateFile的dwFlagsAndAttributes参数必须是一个FILE_FLAG_OVERLAPPED标志来打开设备。这个标志就告诉系统我们想要以异步的方式来访问设备。

为了将I/O请求加入设备驱动程序中，我们必须使用ReadFile和WriteFile函数。
```C++
BOOL ReadFile(
  HANDLE hFile,
  LPVOID lpBuffer,
  DWORD nNumberOfBytesToRead,
  LPDWORD lpNumberOfBytesRead,
  LPOVERLAPPED lpOverlapped
);

BOOL WriteFile( 
  HANDLE hFile, 
  LPCVOID lpBuffer, 
  DWORD nNumberOfBytesToWrite, 
  LPDWORD lpNumberOfBytesWritten, 
  LPOVERLAPPED lpOverlapped
);

当我们调用这两个函数中的任何一个时，函数会检查hFile参数标识的设备是否是用FILE_FLAG_OVERLAPPED标志打开的。如果打开设备时指定了这个标志，那么函数会执行异步设备I/O。

顺便说一下，当调用这两个函数来进行异步I/O的时候，我们通常给lpNumberOfBytesWritten或者lpNumberOfBytesRead赋予NULL，因为我们希望这两个函数在I/O请求完成之前就返回。

因此这时就检查已经传输的字节数是没有意义的。争取充分利用CPU！！！！
```

## 5 接收I/O请求完成通知
