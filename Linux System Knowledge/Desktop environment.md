# Desktop environment
桌面环境（desktop environment DE）是由一堆程序组成的桌面隐喻的实现，它们共享一个通用的图形用户界面（GUI）。
# 一、概述
一个桌面环境将各种组件捆绑在一起，已提供常见的图形用户界面元素，如：图标、工具栏、壁纸、和桌面小部件(desktop widgets)。
此外，大多数桌面环境会包含一组集成的应用程序和程序实例。最终要的是桌面环境提供了自己的窗口[管理器](https://wiki.archlinux.org/index.php/Window_manager),通常可以用另一个兼容的替换。

用户可以通过多种方式任意的配置他们的GUI环境。桌面环境只是提供了完成此任务的完整和方便的方法。请注意，用户可以自由地混合和匹配来自多个桌面环境的应用程序。比如，KDE用户可以安装和运行GNOME应用程序，例如Epiphany web浏览器，如果他/她不喜欢KDE的Konqueror web浏览器。

这种方法有一个缺点，这些桌面环境提供的应用很大程度都是依赖它们自己的桌面环境底层库。因此，从一系列桌面环境安装应用程序将需要安装更多的依赖项。寻求节省磁盘空间的用户通常避免这种混合环境，或者选择仅依赖于少数外部库的替代方案。
此外， DE(desktop environment)提供的应用程序倾向于更好地与其本机环境集成。最直观来看，将环境与不同的小部件工具包混合将导致视觉差异（即，界面将使用不同的图标和小部件样式）。就可用性而言，混合环境的行为可能不同（例如，单击图标与双击图标；拖放功能），可能会导致混乱或意外行为。

在安装桌面环境之前，需要先安装X服务器。有关详细信息，请参见[Xorg](https://wiki.archlinux.org/index.php/Xorg)。一些桌面环境也可能支持[Wayland](https://wayland.freedesktop.org/)作为X的替代品，但其中大多数仍然是实验性的。
# 二、桌面环境列表
## 2.1 官方支持
- Budgie — Budgie is a desktop environment designed with the modern user in mind, it focuses on simplicity and elegance.
https://getsol.us/ || budgie-desktop
- Cinnamon — Cinnamon strives to provide a traditional user experience. Cinnamon is a fork of GNOME 3.
http://developer.linuxmint.com/projects/cinnamon-projects.html[dead link 2020-03-28 ⓘ] || cinnamon
- Deepin — Deepin desktop interface and apps feature an intuitive and elegant design. Moving around, sharing and searching etc. has become simply a joyful experience.
https://www.deepin.org/ || deepin
- Enlightenment — The Enlightenment desktop shell provides an efficient window manager based on the Enlightenment Foundation Libraries along with other essential desktop components like a file manager, desktop icons and widgets. It supports themes, while still being capable of performing on older hardware or embedded devices.
https://www.enlightenment.org/ || enlightenment
- GNOME — The GNOME desktop environment is an attractive and intuitive desktop with both a modern (GNOME) and a classic (GNOME Classic) session.
https://www.gnome.org/gnome-3/ || gnome
- GNOME Flashback — GNOME Flashback is a shell for GNOME 3 which was initially called GNOME fallback mode. The desktop layout and the underlying technology is similar to GNOME 2.
https://wiki.gnome.org/Projects/GnomeFlashback || gnome-flashback
- KDE Plasma — The KDE Plasma desktop environment is a familiar working environment. Plasma offers all the tools required for a modern desktop computing experience so you can be productive right from the start.
https://www.kde.org/plasma-desktop || plasma
- LXDE — The Lightweight X11 Desktop Environment is a fast and energy-saving desktop environment. It comes with a modern interface, multi-language support, standard keyboard short cuts and additional features like tabbed file browsing. Fundamentally designed to be lightweight, LXDE strives to be less CPU and RAM intensive than other environments.
https://lxde.org/ || GTK 2: lxde, GTK 3: lxde-gtk3
- LXQt — LXQt is the Qt port and the upcoming version of LXDE, the Lightweight Desktop Environment. It is the product of the merge between the LXDE-Qt and the Razor-qt projects: A lightweight, modular, blazing-fast and user-friendly desktop environment.
https://lxqt.org/ || lxqt
- MATE — Mate provides an intuitive and attractive desktop to Linux users using traditional metaphors. MATE started as a fork of GNOME 2, but now uses GTK 3.
https://mate-desktop.org/ || mate
- Sugar — The Sugar Learning Platform is a computer environment composed of Activities designed to help children from 5 to 12 years of age learn together through rich-media expression. Sugar is the core component of a worldwide effort to provide every child with the opportunity for a quality education — it is currently used by nearly one-million children worldwide speaking 25 languages in over 40 countries. Sugar provides the means to help people lead fulfilling lives through access to a quality education that is currently missed by so many.
https://sugarlabs.org/ || sugar + sugar-fructose
- UKUI — UKUI is a lightweight Linux desktop environment, developed based on GTK and Qt. UKUI is the default desktop environment for Ubuntu kylin.
https://www.ukui.org/ || ukui
- Xfce — Xfce embodies the traditional UNIX philosophy of modularity and re-usability. It consists of a number of components that provide the full functionality one can expect of a modern desktop environment, while remaining relatively light. They are packaged separately and you can pick among the available packages to create the optimal personal working environment.
https://xfce.org/ || xfce4
## 2.2 非官方支持
# 三、自定义环境
## 3.1 使用不同的窗口管理器
