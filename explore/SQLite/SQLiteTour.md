<h1 align="center">SQLite Source Repository</h1>

此存储库包含[SQLite数据库引擎](https://sqlite.org/),和一些测试脚本
也包括在内。然而，大量的测试脚本和文档都是单独管理的。

## 版本控制

SQLite 源码使用[Fossil](https://www.fossil-scm.org/)进行管理, 一个分布式版本控制系统。它是专门为支持SQLite开发而设计和编写的。更多关于Fossil的内容请移步此链接[Fossil repository](https://sqlite.org/src/timeline)

如果您是在GitHub或者其他Git仓库读到了本文，然后查看镜像。Git镜像中的签入和其他构件的名称与官方的是不同的。签入的官方名称位于已授权镜像的签入注释的页脚中。官方签名也可以在树的根目录下`manifest.uuid`文件。在与SQLite签入进行通信时，请始终使用官方名称，而不是Git名称。

如果您从辅助获取SQLite源代码并希望验证其完整性, 下面的[Verifying Code Authenticity](#vauth)提供了有关如何执行此操作的提示。
## 获取代码

如果您不想使用Fossil来获取源码, 您可以使用tar包或者zip包或者如下[SQLite存档](https://sqlite.org/cli.html\sqlar)

  *  最新主干连接如下：
     [Tarball](https://www.sqlite.org/src/tarball/sqlite.tar.gz),
     [ZIP-archive](https://www.sqlite.org/src/zip/sqlite.zip), or
     [SQLite-archive](https://www.sqlite.org/src/sqlar/sqlite.sqlar).

  *  最新发布如下：
     [Tarball](https://www.sqlite.org/src/tarball/sqlite.tar.gz?r=release),
     [ZIP-archive](https://www.sqlite.org/src/zip/sqlite.zip?r=release), or
     [SQLite-archive](https://www.sqlite.org/src/sqlar/sqlite.sqlar?r=release).

  *  对于其他签入，用适当的分支名称或标记或散列前缀代替前一个项目符号的url中的“release”。或浏览[时间轴](https://www.sqlite.org/src/timeline)要找到所需的签入，请单击其信息页链接，然后单击信息页上的“Tarball”或“ZIP Archive”链接。

如果你想使用Fossil来check out源码目录结构, 首先必须安装Fossil 版本 2.0或者之后的版本。
(提供源tar和预编译的二进制文件[here](https://www.fossil-scm.org/fossil/uv/download.html).  Fossil 是一个独立项目。安装，简单的下载或者构建单独的可执行文件并且在你的环境变量$PATH中部署。然后运行如下命令：
        mkdir ~/sqlite
        cd ~/sqlite
        fossil clone https://www.sqlite.org/src sqlite.fossil
        fossil open sqlite.fossil

然后使用上面的命令设置本地仓库，你也可以更新最新的版本：

        fossil update trunk   ;# latest trunk check-in
        fossil update release ;# latest official release

或者“fossil ui” 方式获取基于web界面的可视化接口来操作。

## 编译
创建一个构建此项目的单独文件夹。这只是一个建议，不要求，这样可以把源代码目录和生成目录分离开。cd 到构建目录然后执行configure脚本，最后执行make。

案例:

        tar xzf sqlite.tar.gz    ;#  Unpack the source tree into "sqlite"
        mkdir bld                ;#  Build will occur in a sibling directory
        cd bld                   ;#  Change to the build directory
        ../sqlite/configure      ;#  Run the configure script
        make                     ;#  Run the makefile.
        make sqlite3.c           ;#  Build the "amalgamation" source file
        make test                ;#  Run some tests (requires Tcl)

想实现其他特性，请查看makefile。

configure脚本使用autoconf2.61和libtool。如果configure脚本。如果配置脚本不适合你，有一个通用的makefile“Makefile.linux-gcc”在源树的顶层目录中可以复制和编辑以满足您的需要。通用makefile 的注释显示需要进行哪些更改。

## 使用MSVC
在Windows上, 所有合适的构建产品否可以用MSVC编译。首先打开与所需编译器版本相关联的命令提示符窗口（例如“Developer command prompt for VS2013”）。接下来，使用NMAKE和提供的“生成文件.msc“建立一个受支持的目标。

案例:

        mkdir bld
        cd bld
        nmake /f Makefile.msc TOP=..\sqlite
        nmake /f Makefile.msc sqlite3.c TOP=..\sqlite
        nmake /f Makefile.msc sqlite3.dll TOP=..\sqlite
        nmake /f Makefile.msc sqlite3.exe TOP=..\sqlite
        nmake /f Makefile.msc test TOP=..\sqlite

这里有几个构建选项可以通过NMAKE命令行方式来设置。
例如:
用WinRT构建, 增加“FOR_WINRT=1”参数在上面"sqlite3.dll"后面。
当编译Debug版本SQLite代码,  可以在上面任意一行编译命令后增加"DEBUG=1"参数。

SQLite不要求运行[Tcl](http://www.tcl.tk/)，但是makefiles(包含在MSVC)要求安装Tcl。
SQLite包含大量生成的代码并且Tcl用来生成大量代码。

## 源码入门
大多数核心源码文件在**src/**子目录中。**src/**文件夹还包含用于构建“testfixture”测试工具的文件。“testfixture”使用的源文件的名称都以“test”开头。

**src/**还包含“shell.c”文件，它是“sqlite3.exe”的主程序[命令行shell](https://sqlite.org/cli.html)以及“tclsqlite.c”实现[Tcl构建](https://sqlite.org/tclsqlite.html)对于SQLite。（历史：SQLite最初是一个Tcl扩展，后来才作为一个独立的库逃到了野外。）

测试脚本和测试项目在**test/**子目录。在其他源存储库中可以找到其他测试代码。请参见[如何测试SQLite](http://www.sqlite.org/testing.html)更多信息。

**ext/**子目录包含扩展代码。
全文搜索引擎位于**ext/fts3**。
R-Tree引擎位于**ext/rtree**。
**ext/misc**子目录包含许多较小的单个文件扩展名，例如REGEXP运算符。
**tool/**子目录包含使用的各种脚本和程序用于生成生成的源代码文件或用于测试或生成辅助程序，如“sqlite3_analyzer（.exe）”。

### 生成的源码文件

SQLite使用的一些C语言源文件是从其他源生成的，而不是由程序员手工输入。本节将总结那些自动生成的文件。

这一节来总结那些自动生成的文件。为了创建所有这些自动生成的文件，简单运行"make target&#95;source"。"make target&#95;source"将创建一个子目录"tsrc/"，然后构建SQLite所需的源文件、手动编辑和自动生成的文件将被放置在"tsrc/"子目录。


The SQLite interface is defined by the **sqlite3.h** header file, which is generated from src/sqlite.h.in, ./manifest.uuid, and ./VERSION.  The [Tcl script](http://www.tcl.tk) at tool/mksqlite3h.tcl does the conversion.
The manifest.uuid file contains the SHA3 hash of the particular check-in and is used to generate the SQLITE\_SOURCE\_ID macro.  
The VERSION file contains the current SQLite version number.  The sqlite3.h header is really just a copy of src/sqlite.h.in with the source-id and version number inserted at just the right spots. Note that comment text in the sqlite3.h file is used to generate much of the SQLite API documentation.  The Tcl scripts used to generate that documentation are in a separate source repository.

The SQL language parser is **parse.c** which is generate from a grammar in
the src/parse.y file.  The conversion of "parse.y" into "parse.c" is done
by the [lemon](./doc/lemon.html) LALR(1) parser generator.  The source code
for lemon is at tool/lemon.c.  Lemon uses the tool/lempar.c file as a
template for generating its parser.
Lemon also generates the **parse.h** header file, at the same time it
generates parse.c.

The **opcodes.h** header file contains macros that define the numbers
corresponding to opcodes in the "VDBE" virtual machine.  The opcodes.h
file is generated by the scanning the src/vdbe.c source file.  The
Tcl script at ./mkopcodeh.tcl does this scan and generates opcodes.h.
A second Tcl script, ./mkopcodec.tcl, then scans opcodes.h to generate
the **opcodes.c** source file, which contains a reverse mapping from
opcode-number to opcode-name that is used for EXPLAIN output.

The **keywordhash.h** header file contains the definition of a hash table
that maps SQL language keywords (ex: "CREATE", "SELECT", "INDEX", etc.) into
the numeric codes used by the parse.c parser.  The keywordhash.h file is
generated by a C-language program at tool mkkeywordhash.c.

The **pragma.h** header file contains various definitions used to parse
and implement the PRAGMA statements.  The header is generated by a
script **tool/mkpragmatab.tcl**. If you want to add a new PRAGMA, edit
the **tool/mkpragmatab.tcl** file to insert the information needed by the
parser for your new PRAGMA, then run the script to regenerate the
**pragma.h** header file.

### 合并

All of the individual C source code and header files (both manually-edited
and automatically-generated) can be combined into a single big source file
**sqlite3.c** called "the amalgamation".  The amalgamation is the recommended
way of using SQLite in a larger application.  Combining all individual
source code files into a single big source code file allows the C compiler
to perform more cross-procedure analysis and generate better code.  SQLite
runs about 5% faster when compiled from the amalgamation versus when compiled
from individual source files.

The amalgamation is generated from the tool/mksqlite3c.tcl Tcl script.
First, all of the individual source files must be gathered into the tsrc/
subdirectory (using the equivalent of "make target_source") then the
tool/mksqlite3c.tcl script is run to copy them all together in just the
right order while resolving internal "#include" references.

The amalgamation source file is more than 200K lines long.  Some symbolic
debuggers (most notably MSVC) are unable to deal with files longer than 64K
lines.  To work around this, a separate Tcl script, tool/split-sqlite3c.tcl,
can be run on the amalgamation to break it up into a single small C file
called **sqlite3-all.c** that does #include on about seven other files
named **sqlite3-1.c**, **sqlite3-2.c**, ..., **sqlite3-7.c**.  In this way,
all of the source code is contained within a single translation unit so
that the compiler can do extra cross-procedure optimization, but no
individual source file exceeds 32K lines in length.

## 它是如何组合在一起

在设计上SQLite是模块化的。
参见[架构描述](http://www.sqlite.org/arch.html)详细信息。

其他有用的文档（有助于理解SQLite的工作原理）包括[文件格式](http://www.sqlite.org/fileformat2.html) 描述, 运行准备好的语句的 [虚拟机](http://www.sqlite.org/opcode.html) 描述[事务处理怎么运行](http://www.sqlite.org/atomiccommit.html), 和[查询计划器概述](http://www.sqlite.org/optoverview.html).

多年来一直致力于优化SQLite，以实现小尺寸和高性能。优化往往会导致复杂的代码。因此，当前的SQLite实现有很多复杂性。它将不是世界上最容易破解的库。

关键文件:

  *  **sqlite.h.in** - 这个文件定义了SQLite库的公共接口。
     读者可以熟悉此接口文件来试着理解SQLite库的内部原理。

  *  **sqliteInt.h** - 这个头文件定义了许多SQLite内部使用的数据对象。
     除了“sqliteInt.h”外，  一些子系统有它自己的头文件。

  *  **parse.y** - 这个文件描述了SQLite用来解析SQL语句的[LALR(1)]        (https://baike.baidu.com/item/LALR%E8%AF%AD%E6%B3%95%E5%88%86%E6%9E%90%E5%99%A8/13025263?fr=aladdin)语法，以及解析过程中每个步骤所采取的操作。

  *  **vdbe.c** - 这个文件是一个虚拟机用来运行准备好的语句。
     有许多以"vdbe"开头的帮助文件。VDBE可以访问vdbeInt.h，它定义了内部对象。
     其余带有VDBE的SQLite接口通过接口文件vdbe.h定义。

  *  **where.c** - 这个文件 (和它的帮助文件命名都是以“where*.c”的方式命名)
     分析WHERE子句并生成虚拟机代码以高效的运行查询语句。
     这个文件有时被称为"查询优化器"。
     它有自己的私有头文件，whereInt.h,它定义了内部使用的数据对象。

  *  **btree.c** - 这个文件包含了SQLite使用的存储引擎B-tree(多路搜索树，并不是二叉的)的实现。
     与系统其余部分的接口由“btree.h”定义。
     “btreeInt.h”头定义btree.c内部使用的对象，而不是发布到系统的其他部分。

  *  **pager.c** - 这个文件包含了“pager”实现，这个模块是事物的实现。
     “pager.h”头文件定义了pager.c和系统其余部分之间的接口。

  *  **os_unix.c** and **os_win.c** - 这两个文件使用运行时可插拔VFS接口实现SQLite和底层操作系统之间的接口。

  *  **shell.c.in** - 这个文件不是SQLite库的核心文件。
     这是在链接sqlite3.a时生成“sqlite3.exe”命令行shell的文件。
     在构建过程中，“shell.c.in”文件被转换为“shell.c”。

  *  **tclsqlite.c** - 这个文件是Tcl构建SQLite的实现。
     它不是SQLite核心库的一部分。但是大多数的测试使用Tcl编写的。
     所以Tcl语言很重要。
     
  *  **test*.c** - 文件在src/目录，以test开头用来构建testfixture.exe程序。testfixture.exe 程序是加强的Tcl shell。
     testfixture.exe 程序运行test/文件夹中的脚本来验证核心SQLite代码。当你键入"make test"命令 testfixture 程序（和一些其他测试程序）将被构建和运行。

  *  **ext/misc/json1.c** - 这个文件实现了构建到SQLite中的各种JSON函数。

还有许多其他源文件。每一个都有一个简洁的标题注释，描述了它在更大系统中的用途和作用。

<a name="vauth"></a>

## 验证代码真实性

The `manifest` file at the root directory of the source tree
contains either a SHA3-256 hash (for newer files) or a SHA1 hash (for 
older files) for every source file in the repository.
The SHA3-256 hash of the `manifest`
file itself is the official name of the version of the source tree that you
have. The `manifest.uuid` file should contain the SHA3-256 hash of the
`manifest` file. If all of the above hash comparisons are correct, then
you can be confident that your source tree is authentic and unadulterated.

The format of the `manifest` file should be mostly self-explanatory, but
if you want details, they are available
[here](https://fossil-scm.org/fossil/doc/trunk/www/fileformat.wiki#manifest).

## 联络

具有地理分布备份的SQLite主页 [http://www.sqlite.org/](http://www.sqlite.org/)
[http://www2.sqlite.org/](http://www2.sqlite.org) 和[http://www3.sqlite.org/](http://www3.sqlite.org).
