<h1 align=center> Configure VS Code for Microsoft C++ </h1>

在本教程中，您可以配置Visual Studio Code在Windows上使用微软Visual C++编译器和调试器。

在配置了VS Code之后，你将使用VS Code编译和调试一个简单的Hello World程序。本教程不教你微软C++工具集或C++语言的细节。
对于这些主题，网上有很多好的资源。

# 前提
为了成功编译这个案例，你必须做一下事情：
1. 安装VS Code
2. 安装C/C++ extension for VS Code。

![](https://code.visualstudio.com/assets/docs/cpp/cpp/cpp-extension.png)

(Ctrl+Shift+X)

3. 安装微软C++(MSVC)编译器工具集
如果你已经安装了最新的Visual Studio版本，从开始菜单打开Visual Studio Installer，并验证C++工作负载是否被选中。
如果没有安装，请选中该框并单击安装程序中的 "修改" 按钮。 

你也可以通过仅仅通过安装C++ Build Tools，without a full Visual Studio IDE instanllation。从[Visual Studio 下载页面](https://visualstudio.microsoft.com/downloads/#other), 向下滑动直到看到 **All downloads** 下面的 **Tool for Visual Studio** 
选项，选择**Build Tools for Visual Studio** 下载。

![](https://code.visualstudio.com/assets/docs/cpp/msvc/build-tools-for-vs.png)

这将登录到Visual Studio Installer， 将会bring up 一个窗口，显示Visual Studio Tool 工作负载。检测 C++ build tools工作负载并选中安装。

![](https://code.visualstudio.com/assets/docs/cpp/msvc/cpp-build-tools.png)

>注意：
> 您可以使用Visual Studio Build Tools 的C++工具集结合Visual Studio Code编译、构建和验证任何C++代码库，只要您还拥有一个有效的VisualStudio许可证（社区、专业或企业），您就可以积极地使用它来开发C++代码库。



# 检查你的微软 Visual C++ 是否安装成功
若要从命令行或VS Code中使用MSVC，必须从 Developer Command Prompt for Visual Studio 下运行。 普通的shell(如PowerShell、Bash或Windows命令提示符) 没有设置必要的环境变量路径。

在Windows开始菜单键入"developer", 打开 VS的Developer Command Prompt , 之后可以在建议列表中看到它。 确切的名称取决于您安装的Visual Studio 版本和Visual Studio Build Tools。 点击打开命令提示符对话框。

![](https://code.visualstudio.com/assets/docs/cpp/msvc/developer-cmd-prompt-menu.png)

你可以测试一下C++编译器cl.exe，键入"cl", 你可以看到版权信息和版本信息，还有使用描述。

![](https://code.visualstudio.com/assets/docs/cpp/msvc/check-cl-exe.png)

如果 Developer Command Prompt 使用 BuildTools位置作为起始目录(您不希望将项目放在那里)，请在开始创建新项目之前导航到您的用户文件夹(C:\users\{yourusername}\)。

# 创建一个Hello World

# 代码提示
# 构建Helloworld.cpp
# 调试helloworld.cpp
# 单步查询代码
# Set a watch
# C/C++ 配置
# Troubleshooting
# Next steps
