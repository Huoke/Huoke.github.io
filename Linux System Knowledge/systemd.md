# systemd System and Service Manager
## 一、What is this
systemd是linux的一套基础构件。它提供了一个system，以PID=1运行来启动系统其余部分的服务管理器。systemd提供了积极的并行化能力。使用socket和D-Bus触发来启动服务。提供按需启动守护进程。使用Linux控制组来跟踪正在运行的进程。维护挂载点和自动挂载点。实现一个基于事务依赖关系的服务控制逻辑。
