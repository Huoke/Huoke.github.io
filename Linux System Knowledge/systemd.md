# systemd System and Service Manager
## What is this
systemd是linux的一套基础构件。它提供了一个system，以PID=1运行来启动系统其余部分的服务管理器。systemd提供了积极的并行化能力。使用socket和D-Bus触发来启动服务。提供按需启动守护进程。

使用Linux [控制组](https://wiki.archlinux.org/index.php/Cgroups) 来跟踪正在运行的进程。维护挂载点和自动挂载点。实现一个基于事务依赖关系的服务控制逻辑。

systemd支持Sys V和LSB(全称为 Linux 标准基础核心规范) init脚本，并且替代了[sysvinit风格](https://zh.opensuse.org/openSUSE:Packaging_init_scripts)的引导脚本。

其他部分包括日志守护进程，用于控制基本系统配置的实用程序，如主机名、日期、区域设置，维护登录用户和正在运行的容器和虚拟机、系统帐户、运行时目录和设置的列表，和守护程序来管理简单的网络配置、网络时间同步、日志转发和名称解析。

## 一、systemctl 的基本用法
主命令时systemctl，它是反省和控制systemd的主命令。它的一些用途是检查系统状态(system
 state)和管理系统和服务(managing the system and services)。有关更多详细信息，请参阅[systemctl](https://jlk.fjfi.cvut.cz/arch/manpages/man/systemctl.1)。
 
 >**提示**：
 > - 下面的所有systemctl命令与 -H user@host 开关一起用于控制远程计算机上的一个systemd实例。将使用SSH连接到远程systemd实例。
 > - Plasma用户可以安装systemd-kcmAUR作为systemctl的图形前端。安装后，在系统管理(System administration)下会有该模块。
 
### 1.1 分析系统状态
显示系统状态使用：
```shell
$ systemctl status
```
列出运行单位：
```shell
$ systemctl
```
或者
```shell
systemctl list-units
```
列出失败的单元：
```shell
$ systemctl --failed
```
可用的单元文件可以在/usr/lib/systemd/system/和/etc/systemd/system/（后者优先）。列出已安装的单元文件：
```shell
systemctl list-unit-files
```
显示一个进程的cgroup slice，内存和父进程
```shell
systemctl status pid
```
### 1.2 使用 Units
单元可以是，例如，服务（.service）、装入点（.mount）、设备（.device）或套接字（.socket）。使用systemctl时，通常必须指定单元文件的完整名称，包括其后缀，例如sshd.socket。但是，在以下systemctl命令中指定单位时有一些简短的格式：
- 如果不指定后缀，systemctl默认为是.service。例如，netctl和netctl.service是等价的。
- 挂载点将自动转换为相应的挂载单元。例如，指定/home等同于home.mount。
- 与挂载点类似，设备会自动转换为相应的.device单元，因此指定/dev/sda2等同于dev-sda2.device。
参考[systemd.unit(5)的详细信息](https://jlk.fjfi.cvut.cz/arch/manpages/man/systemd.unit.5)
> 注意：
>
## 二、Writing unit files
## 三、Targets
## 四、Timers
## 五、Mounting
## 六、systemd-sysvcompat
## 七、Tips and tricks
## 八、Troubleshooting

