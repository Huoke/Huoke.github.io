# 1、介绍
## 1.1、版权和许可
## 1.2、免责声明
## 1.3、学分/贡献者
## 1.4、反馈
## 1.5、翻译
# 2、TCP Keepalive HOW TO
keep TCP alive。这意味着您将能够检查已连接的套接字（也称为TCP套接字），并确定连接是否仍在运行或是否已断开。
## 2.1、什么是TCP keepalive？
keepalive概念很简单:当我们建立一个CTP连接时，会在内核中关联一组计时器。其中一些计时器就会专门来处理keepalive。当keepalive定时器到0时，会向对端发送一个keepalive探测包，它是没有数据的，并且ACK便签打开。由于TCP/IP规范是一种重复的ACK、面向流的协议。所以你将收到来自远方主机的回复(没有设置keepalive的TCP/IP)，没有数据的ACK确认包。

如果系统收到keepalive探测包返回的ACK包，那么久可以确定网络正常运行，从而不必担心在自己的应用层中没有实现心跳来判断网络状况。实际上TCP是允许处理没有数据的包的，所以没有数据的包对用户程序来说没有危害。

这个流程是很有用的，因为如果对端失去链接：比如路由器重启，后者网线拔掉，或者对端的机器断电。这种情况下就可以判断网络连接已经断开。当然有数据发送时可以，没有数据发送时这种网络监测就更重要了。

这里需要注意哦keepalive探测包返回值：
- 有返回值
- 无返回值
## 2.2、为什么使用TCP keepalive？
没有认识keepalive时，你可以过得很快乐，如果你读到这篇文章，你可能会试图理解keepalive是否是解决你问题的一种可能的方法。要么是这样，要么你真的没有什么更有趣的事要做了，那也没关系。:)

Keepalive是非侵入性的，在大多数情况下，如果你有疑问，你可以打开它，而不会有做错事的风险。但请记住，它会产生额外的网络流量，这会对路由器和防火墙产生影响。这就是keepalive攻击！

一句话：使用前先想好！

下一节我们讨论keepalive的两个目的:
- 检测死对等点(这里需要在仔细解释一下)
- 防止由于网络不活动而断开的连接(这里需要在仔细解释一下)
## 2.3、检查死对等点
keepalive探测包可以通知你当对端挂掉时，对端挂掉有几中可能: 
1. 比如内核错误(kernel panic)或者粗暴的结束点对端进程(向任务管理器中直接结束掉进程，有些系统会提供FIN包，有些则不会)。
2. 另一种情况，需要keepalive来检测对端是否挂掉了，比如对端服务任然运行，但是它与你之间的网络通道断开了。WIFI路由器就有这个问题。这种情况下如果网络不在了，相当于对端挂了。这就是其中的一个问题，正常的TCP操作是检测不出网络连接状态的。

考虑A和B一个简单的TCP连接：建立连接的三次握手，一个SYN段从A到B，SYN/ACK从B回到A，最后一个ACK从A到B。此时A和B处于稳定状态：ESTABLISHED。接下来就是等待方某一方通过这个稳定的通道来发送数据。
```vb
    _____                                                     _____
   |     |                                                   |     |
   |  A  |                                                   |  B  |
   |_____|                                                   |_____|
      ^                                                         ^
      |--->--->--->-------------- SYN -------------->--->--->---|
      |---<---<---<------------ SYN/ACK ------------<---<---<---|
      |--->--->--->-------------- ACK -------------->--->--->---|
```


此时问题出现了：从B上拔下电源，B会立即断开，不会通过网络发送任何信息来通知A连接即将断开。A这边，已经准备好接收数据了，还不知道B已经崩溃了。
```vb
    _____                                                     _____
   |     |                                                   |     |
   |  A  |                                                   |  B  |
   |_____|                                                   |_____|
      ^                                                         ^
      |--->--->--->-------------- SYN -------------->--->--->---|
      |---<---<---<------------ SYN/ACK ------------<---<---<---|
      |--->--->--->-------------- ACK -------------->--->--->---|
      |                                                         |
      |                                       system crash ---> X
```


恢复B的电源，等待系统重新启动。A和B重新回到原来的连接，A知道和B的网络任然是建立连接的，但B不知道。当A试图通过这个死连接向B发送数据时，B使用RST数据包进行回复，导致A最终关闭连接。
```vb
    _____                                                     _____
   |     |                                                   |     |
   |  A  |                                                   |  B  |
   |_____|                                                   |_____|
      ^                                                         ^
      |--->--->--->-------------- SYN -------------->--->--->---|
      |---<---<---<------------ SYN/ACK ------------<---<---<---|
      |--->--->--->-------------- ACK -------------->--->--->---|
      |                                                         |
      |                                       system crash ---> X
      |
      |                                     system restart ---> ^
      |                                                         |
      |--->--->--->-------------- PSH -------------->--->--->---|
      |---<---<---<-------------- RST --------------<---<---<---|
      |                                                         |
```
当另一端不可达，keepalive可以知会到你，而没有任何误报的风险。实际上如果问题发生在两端之间的网络，keepalive定时器在等待一段时间后继续重试，在将连接判断为断开前发送keepalive包。
## 2.4、防止由于网络不活动而断开连接(什么意思？)
keepalive的另一个目的是防止因为不活动而断开网络通道。 比如下面：
当你运行的服务在NAT代理或者防火墙后面时(什么鬼)，无缘无故的就断开连接。这是由代理服务器和防火墙中的连接跟踪过程引起的，它能追踪通过它们的所有连接。由于这些机器的物理限制，它们只能在内存中保留有限数量的连接。最常见的逻辑策略是保留最新的连接，并首先丢弃旧的和非活动的连接。

返回到之前A和B连接状态，只是在中间加了一个NAT服务器/代理服务器。
```vb
    _____           _____                                     _____
   |     |         |     |                                   |     |
   |  A  |         | NAT |                                   |  B  |
   |_____|         |_____|                                   |_____|
      ^               ^                                         ^
      |--->--->--->---|----------- SYN ------------->--->--->---|
      |---<---<---<---|--------- SYN/ACK -----------<---<---<---|
      |--->--->--->---|----------- ACK ------------->--->--->---|
      |               |                                         |
```
通道打开，代理等待事件发生，然后将其传递到另一个对等方。如果这个事件的验证信息需要等一段时间才返回呢？这个时间超出了代理/防火墙的判断策略呢？如果这段时间过后我们发生验证数据，结果代理没有保留我们的连接了，此时连接就中断了。
```vb
    _____           _____                                     _____
   |     |         |     |                                   |     |
   |  A  |         | NAT |                                   |  B  |
   |_____|         |_____|                                   |_____|
      ^               ^                                         ^
      |--->--->--->---|----------- SYN ------------->--->--->---|
      |---<---<---<---|--------- SYN/ACK -----------<---<---<---|
      |--->--->--->---|----------- ACK ------------->--->--->---|
      |               |                                         |
      |               | <--- connection deleted from table      |
      |               |                                         |
      |--->- PSH ->---| <--- invalid connection                 |
      |               |                                         |
```
由于防火墙/NAT的策略通常是把刚到的包的连接放在列表的顶部，在需要删除条目时删除队列中最后的一个连接，所以定期的通过网络发送数据包是一种很好的方法，使得连接始终处在顶部，避免被从队列中删除的风险。
>
>1. 内核错误(Kernel panic)是指操作系统在监测到内部的致命错误，并无法安全处理此错误时采取的动作。这个概念主要被限定在Unix以及类Unix系统中；对>于MicrosoftWindows系统，等同的概念通常被称为蓝屏死机。

# 3、在Linux下使用TCP的keepalive
Linux内置了对keepalive的支持。您需要启用TCP/IP网络才能使用它。您还需要procfs支持和sysctl支持才能在运行时配置内核参数。
涉及keepalive设置需要三个用户驱动变量：
- tcp_keepalive_time
  发送的最后一个数据包和首次发送keepalive探测包之间的时间间隔; 在连接被标记为需要keepalive之后不再使用这个计数器。
- tcp_keepalive_intvl
  keepalive探测包之间的时间间隔，在这期间不管连接发生什么改变。
- tcp_keepalive_probes
  在连接失效并通知应用层之前，要发送的未确认探测包数量。

*记住：Linux默认是不支持keepalive的，即使内核中有配置。程序必须使用setsockopt接口为其套接字获取keepalive控件。
实现keepalive的程序相对较少，但是您可以按照本文档后面的说明轻松地为大多数程序添加keepalive的支持(控件)。*
## 3.1、配置内核参数
有两种方法可以通过用户层命令在内核中配置keepalive参数：
- procfs接口
- sysctl接口
我们主要讨论如何在procfs接口上实现这一点，因为它是最常用、推荐和最容易理解的。sysctl接口，特别是关于sysctl（2）syscall而不是sysctl（8）工具的接口，仅用于背景知识。
### 3.1.1、procfs接口
此接口需要同时在内核中安装sysctl和procfs，procfs需要安装/挂载到文件系统的某个位置(通常在/proc,如下案例所示)。您可以通过在/proc/sys/net/ipv4/目录中“catting”文件来读取实际参数的值：
```shell
  # cat /proc/sys/net/ipv4/tcp_keepalive_time
  7200
  
  # cat /proc/sys/net/ipv4/tcp_keepalive_intvl
  75

  # cat /proc/sys/net/ipv4/tcp_keepalive_probes
  9
```
前两个参数单位是秒，最后一个是纯数。这意味着keepalive程序在发送第一个keepalive探测之前需要等待两个小时（7200秒），然后每75秒重新发送一次。
如果连续九次未收到ACK响应，则将连接标记为断开。

修改这个值很简单：您需要在文件中写入新值。假设您决定配置主机，以便keepalive在通道不活动十分钟后启动，然后每隔一分钟发送探测。由于网络主干的高度不稳定性和间隔的低值，假设您还希望将探测数增加到20。下面就是我们修改的设置：
```shell
  # echo 600 > /proc/sys/net/ipv4/tcp_keepalive_time

  # echo 60 > /proc/sys/net/ipv4/tcp_keepalive_intvl

  # echo 20 > /proc/sys/net/ipv4/tcp_keepalive_probes
```
要确保这些修改都有效，还是重新检查一下文件，确认这些新值取代了旧值。

*记住：procfs处理特殊文件，不能对它们执行任何操作，因为它们只是内核空间中的一个接口，而不是真正的文件，所以在使用它们之前，请尝试使用脚本，并尝试使用简单的访问方法，如前所示。*

可以通过sysctl（8）工具访问接口，指定要读或写的内容：
```shell
  # sysctl \
  > net.ipv4.tcp_keepalive_time \
  > net.ipv4.tcp_keepalive_intvl \
  > net.ipv4.tcp_keepalive_probes
  net.ipv4.tcp_keepalive_time = 7200
  net.ipv4.tcp_keepalive_intvl = 75
  net.ipv4.tcp_keepalive_probes = 9
```
注意：sysctl names 和 procfs paths非常相似，使用sysctl（8）的-w开关执行写操作：
```shell
  # sysctl -w \
  > net.ipv4.tcp_keepalive_time=600 \
  > net.ipv4.tcp_keepalive_intvl=60 \
  > net.ipv4.tcp_keepalive_probes=20
  net.ipv4.tcp_keepalive_time = 600
  net.ipv4.tcp_keepalive_intvl = 60
  net.ipv4.tcp_keepalive_probes = 20
```
注意：使用的是sysctl(8)而不是sysctl(2)系统调用。因为读写都直接在procfs子树中读写，所以您需要在内核中启用procfs并将其安装到文件系统中，就像直接访问procfs接口中的文件一样。Sysctl（8）只是做同样事情的另一种方法。
### 3.1.2、sysctl接口
## 3.2、重启来持久化设置
有几种方法可以在每次启动时重新配置系统。首先，每一个Linux 发行版都有自己的init脚本集被称为init(8)。最常见的配置包括/etc/rc.d/目录，或者/etc/init.d目录。在任何情况下你都可以在任何启动脚本中设置参数，因为每次程序需要时都会重读keepalive配置参数。因此，如果在连接任处于启动状态时修改tcp_keepalive_intvl的值，内核将使用最新设置的值。

逻辑上放置初始化命令位置有三个地方：
- 首先是网络的配置。
- 第二个是rc.local脚本，通常包含在所有发型版中，它被称为用户配置设置完成的地方。
- 第三个位置可能已经存在于您的系统中。回到sysctl（8）工具，您可以看到-p开关从/etc/sysctl.conf配置文件加载设置。在许多情况下，In it脚本已经执行了sysctl-p（您可以在配置目录中“grep”它以进行确认），因此您只需在/etc/sysctl.conf中添加行即可在每次启动时加载它们。有关sysctl.conf（5）语法的更多信息，请参阅手册页。
# 4、程序实例
本节讨论如何通过编程来创建使用keepalive的应用程序。它要求你有C编程和网络概念方面的知识。首先你得熟悉sockets，并且有与应用程序相关方面的了解。
## 4.1、当你的代码需要keepalive支持时
并非所有的网络应用程序都需要keepalive支持。请记住，它是TCP keepalive支持。所以，正如您所想象的，只有TCP套接字可以利用它。在编写应用程序时，您可以做的最漂亮的事情是使它尽可能可定制，而不是强制决定。

如果考虑到用户体验，应该实现keepalive，让用户通过命令行或者配置文件的方式来决定打开或者关闭使用这个功能。
## 4.2、setsockopt功能调用
要给特定的套接字启用keepalive，只需要在套接字本身上设置特定的套接字选项。函数原型如下:
```c
  int setsockopt(int s, int level, int optname,
                 const void *optval, socklen_t optlen)
```
第一个参数是socket，以前是用socket（2）创建的；第二个参数必须是SOL_socket，第三个参数必须是SO_KEEPALIVE。第四个参数必须是布尔整数值，表示要启用该选项，而最后一个参数是之前传递的值的大小。

根据手册，成功时返回0，错误时返回-1（并设置errno）。

在写程序时，有三个socket选项可以给keepalive设置。它们都使用SOL_TCP级别代替SOL_SOCKET级别，并且它们只覆盖当前套接字的系统范围变量。
- TCP_KEEPCNT: overrides tcp_keepalive_probes
- TCP_KEEPIDLE: overrides tcp_keepalive_time
- TCP_KEEPINTVL: overrides tcp_keepalive_intvl
## 4.3、代码案例
这是一个创建套接字的小示例，显示keepalive被禁用，然后启用它并检查该选项是否有效设置。
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main(void);

int main()
{
   int s;
   int optval;
   socklen_t optlen = sizeof(optval);

   /* Create the socket */
   if((s = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP)) < 0) {
      perror("socket()");
      exit(EXIT_FAILURE);
   }

   /* Check the status for the keepalive option */
   if(getsockopt(s, SOL_SOCKET, SO_KEEPALIVE, &optval, &optlen) < 0) {
      perror("getsockopt()");
      close(s);
      exit(EXIT_FAILURE);
   }
   printf("SO_KEEPALIVE is %s\n", (optval ? "ON" : "OFF"));

   /* Set the option active */
   optval = 1;
   optlen = sizeof(optval);
   if(setsockopt(s, SOL_SOCKET, SO_KEEPALIVE, &optval, optlen) < 0) {
      perror("setsockopt()");
      close(s);
      exit(EXIT_FAILURE);
   }
   printf("SO_KEEPALIVE set on socket\n");

   /* Check the status again */
   if(getsockopt(s, SOL_SOCKET, SO_KEEPALIVE, &optval, &optlen) < 0) {
      perror("getsockopt()");
      close(s);
      exit(EXIT_FAILURE);
   }
   printf("SO_KEEPALIVE is %s\n", (optval ? "ON" : "OFF"));

   close(s);

   exit(EXIT_SUCCESS);
}
```
# 5、增加对第三方软件的支持
