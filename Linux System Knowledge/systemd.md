# systemd System and Service Manager
## 一、What is this
systemd是linux的一套基础构件。它提供了一个system，以PID=1运行来启动系统其余部分的服务管理器。systemd提供了积极的并行化能力。使用socket和D-Bus触发来启动服务。提供按需启动守护进程。

使用Linux [控制组](https://wiki.archlinux.org/index.php/Cgroups) 来跟踪正在运行的进程。维护挂载点和自动挂载点。实现一个基于事务依赖关系的服务控制逻辑。

systemd支持Sys V和LSB(全称为 Linux 标准基础核心规范) init脚本，并且替代了[sysvinit风格](https://zh.opensuse.org/openSUSE:Packaging_init_scripts)的引导脚本。

其他部分包括日志守护进程，用于控制基本系统配置的实用程序，如主机名、日期、区域设置，维护登录用户和正在运行的容器和虚拟机、系统帐户、运行时目录和设置的列表，和守护程序来管理简单的网络配置、网络时间同步、日志转发和名称解析。

## 二、systemctl 的基本用法
主命令时systemctl，它是反省和控制systemd的主命令。它的一些用途是检查系统状态(system
 state)和管理系统和服务(managing the system and services)。有关更多详细信息，请参阅[systemctl](https://jlk.fjfi.cvut.cz/arch/manpages/man/systemctl.1)。
 
 >**提示**：
 - 下面的所有systemctl命令与 -H user@host 开关一起用于控制远程计算机上的一个systemd实例。将使用SSH连接到远程systemd实例。
 - Plasma用户可以安装systemd-kcmAUR作为systemctl的图形前端。安装后，在系统管理(System administration)下会有该模块。
 
