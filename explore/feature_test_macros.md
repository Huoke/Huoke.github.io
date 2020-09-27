<h1 align="center">特性测试宏<h1>

允许程序员在源代码中使用特性测试宏控制编译出的程序所包含的特性。
使用gcc -ansi 编译出的程序包含ISO C标准定义的一些特性。

# 描述
特性宏允许程序员控制编译出的程序所包含的特性，这些特性定义在系统头文件中。

>注意：
为了生效，一个测试宏必须在包含任何头文件之前被定义。这里可以在命令选项中(cc -DMACRO=value)或者在源码文件开头的头文件前引入特性宏来实现。

强调的是必须在包含任何存在的头文件之前定义宏，因为头文件可以自由的包含彼此。因此，例如，下面的例子(POSIX是允许这样骚操作的)：
    #include <abc.h>
    #define _GUN_SOURCE
    #include <xys.h>
    
定义_GUN_SOURCE宏但是没有效果，原因就是头文件<abc.h>中包含了<zys.h>，使得编译出的程序没有达到想要的特性。

一些特性宏对于创建可移植的应用程序时非常有用的，通过禁用/防止(preventing)非标准定义被暴露。

其他宏可用于公开的非标准定义默认是不暴露的。

每个特性测试宏的详细效果描述可以通过<features.h>头文件来确定。

> 注意：
应用程序不需要直接包含<features.h>文件，事实上(indeed)，极其不鼓励这样做(Doing so is actively discouraged)，见注释:

**特性测试宏的规格要求在manual pages**
当函数需要定义特性测试宏时，man手册的SYNOPSIS通常包含以下形式的注释(例如acct(2) 手册页):
    #include <unistd.h>
    
    int acct(const char *filename);
    glibc要求特性测试宏：
    acct(): _BSD_SOURCE || (_XOPEN_SOURCE && _XOPEN_SOURCE < 500)
**||** 的目的是为了从<unistd.h>中获取acct的声明， 必须规定在任何头文件之前来定义宏:
    #define _BSD_SOURCE
    #define _XOPEN_SOURCE /* or any value < 500 */
    
或者(Alternatively), 等价的定义也可以在编译选项中包含:
    gcc -D_BSD_SOURCE
    gcc -D_XOPEN_SOURCE   # or any value < 500

注意：如下所述，默认情况下定义了一些特性测试宏。
因此可能并不总是必须明确地指定在SYNOPSIS中显示的特性测试宏。

在少数情况下, 手册使用一个缩写(shorthand)来表示需要特性测试宏(来看案例:)
    #include _GUN_SOURCE
    #include <fcntl.h>
    
    ssize_t readahead(int fd, off64_t *offset, size_t count);
    
这种格式只适用于只有一个特性测试宏可以用来公开函数声明的情况，而该宏在默认情况下没有定义。


# 注释
