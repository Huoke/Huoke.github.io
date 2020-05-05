# 15.6 XSI IPC
有三种称为 XSI(System Interface and Headers 代表一种Unix系统的标准)IPC 的IPC: 消息队列、信号量和共享存储器。它们之间有很多相似之处。
>提示：
> XSI IPC 函数时紧密地基于System V 的IPC函数。这三种类型的XSI IPC源自1970年的一种称为"Columbus UNIX"的AT&T内部版本。后来被添加到System V上。由于XSI IPC
不使用文件系统命名空间，而是构造了它们自己的命名空间，为此常常受到批评

## 15.6.1 标识符和键
