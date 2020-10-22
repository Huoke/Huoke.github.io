<h1 align=center> ulimit -t 引起的kill场景分析</h1>

## 1、现象
某台机器ulimit -t 是300，程序占用CPU300秒后收到自己发送的kill信号，自杀！
>>-t <CPU时间> 　指定CPU使用时间的上限，单位为秒。-t The maximum amount of cpu time in seconds

## 2、验证
运行一个死循环程序来持续消耗CPU时间，同时把进程的最大CPU消耗时间设定为180秒，我们看看在180秒这个时间点，进程是否会被杀掉。
```c
ulimit -t 180
ulimit -t
180

vi test.c
#include <stdio.h>
int main()
{
    while(1) {
    
    }
    return 0;
}

gcc test.c
time ./a.out
Killed

real	3m0.029s
user	2m59.966s
sys	0m0.007s
```
可见，3min后我们的测试程序确实被杀了，dmesg也没说什么原因被杀。

写一个stap脚本来确定到底谁杀死了我们的进程
```stap
vi probe.stap
probe signal.send {
    if (sig_name == "SIGKILL")
        printf("%s was sent to %s (pid:%d) by %s uid:%d\n", sig_name, pid_name, sig_pid, execname(), uid())
}

>不知道为什么，在arm机器上会出现这个问题:
>semantic error: while resolving probe point: identifier 'kernel' at /usr/share/systemtap/tapset/linux/signal.stp:73:37
        source: probe __signal.send.send_sigqueue = kernel.function("send_sigqueue")
                                                    ^

semantic error: missing arm64 kernel/module debuginfo [man warning::debuginfo] under '/lib/modules/4.19.0-arm64-server/build'

semantic error: while resolving probe point: identifier '__signal' at :40:7
        source: 		    __signal.send.send_sigqueue,
                		    ^

semantic error: no match

Pass 2: analysis failed.  [man error::pass2]
Number of similar error messages suppressed: 1.
Rerun with -v to see them.
Tip: /usr/share/doc/systemtap/README.Debian should help you get started.

正常是这样结果:
sudo stap prob.stap
SIGKILL was sent to a.out (pid:xxxx) by a.out uid:xxxx
```
我们能捕获到是谁发出的kill信号。

查看内核机制：
```c
/kernel/posix-cpu-timers.c:1139
if (psecs >= sig->rlim[RLIMIT_CPU].rlim_max) {
             /*                                                                                                                                              
              * At the hard limit, we just die.                                                                                                              
              * No need to calculate anything else now.                                                                                                      
              */
             __group_send_sig_info(SIGKILL, SEND_SIG_PRIV, tsk);
             return;
     }
     if (psecs >= sig->rlim[RLIMIT_CPU].rlim_cur) {
             /*                                                                                                                                              
              * At the soft limit, send a SIGXCPU every second.                                                                                              
              */
             __group_send_sig_info(SIGXCPU, SEND_SIG_PRIV, tsk);
             if (sig->rlim[RLIMIT_CPU].rlim_cur
                 < sig->rlim[RLIMIT_CPU].rlim_max) {
                     sig->rlim[RLIMIT_CPU].rlim_cur++;
 }
     }
```
内核的代码解释的很清楚，超过硬CPU限制就简单粗暴的让进程自杀。
