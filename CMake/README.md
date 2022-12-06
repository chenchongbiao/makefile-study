# 原视频链接

[基于VSCode和CMake实现C/C++开发 | Linux篇](https://www.bilibili.com/video/BV1fy4y1b7TC/?spm_id_from=333.337.search-card.all.click&vd_source=ebf9103aa7bb680610fdd7124d317e9d)

# 开发环境搭建

## 编译器 调试器安装

安装GCC GDB

```bash
# 更新软件源
sudo apt update
# 安装编译器和调试器，
sudo apt install build-essential gdb
```

安装成功确认

```bash
gcc --version
g++ --version
gdb --version
```

## CMake安装

安装cmake

```bash
sudo apt install cmake
```

安装成功确认

```bash
cmake --version
```

# GCC编译器

1.GCC编译器支持编译Go、Objective-C、Objective-C++、Fortran等程序

2.Linux开发C\C++一定需要熟悉GCC

3.VSCode是通过调用GCC编译器来实现C\C++的编译工作

实际使用中：

- 使用gcc指令编译C代码
- 使用g++指令编译C++代码

## 编译过程

1.预处理-Pre-Processing

```bash
# -E 选项指示编译器仅对输入文件进行预处理
g++ -E test.cpp -o test.i
```

2.编译-Compiling

```bash
# -S 编译选项告诉g++ 在为C++代码产生了编译语言文件后停止编译
# g++ 产生的汇编语言文件的缺省扩展名 .s
g++ -S test.i -o test.s
```

3.汇编-Assembling

```bash
# -c 选项告诉g++ 进把源码编译为机器语言的目标代码
# 缺省时 g++ 建立的模具表代码文件哟一个 .o 的扩展名
g++ -c test.s -o test.o
```

4.l链接-Linking

```bash
# -o 编译选项来为将产生的可执行文件用指定的文件名
g++ test.o -o test
```

## g++重要编译参数

1.-g 编译带调试信息的可执行文件

```bash
# -g 选项告诉 GCC 产生能被 GNU 调试器GDB使用的调试信息，以调试程序。

# 产生带调试信息的可执行文件test
g++ -g test.cpp
```

2.-O[n] 优化源代码

```bash
## 所谓优化，例如省略掉代码中从未使用过的变量、直接将常量表达式用结果值代替等等，这些操作
会缩减目标文件所包含的代码量，提高最终生成的可执行文件的运行效率。

# -O 选项告诉 g++ 对源代码进行基本优化。这些优化在大多数情况下都会使程序执行的更快。 -O2 
选项告诉 g++ 产生尽可能小和尽可能快的代码。 如-O2，-O3，-On（n 常为0–3）
# -O 同时减小代码的长度和执行时间，其效果等价于-O1
# -O0 表示不做优化
# -O1 为默认优化
# -O2 除了完成-O1的优化之外，还进行一些额外的调整工作，如指令调整等。
# -O3 则包括循环展开和其他一些与处理特性相关的优化工作。
# 选项将使编译的速度比使用 -O 时慢， 但通常产生的代码执行速度会更快。

# 使用 -O2优化源代码，并输出可执行文件
g++ -O2 test.cpp
```

3.-l 和 -L 指定库文件 | 指定库文件路径

```bash
# -l参数(小写)就是用来指定程序要链接的库，-l参数紧接着就是库名
# 在/lib和/usr/lib和/usr/local/lib里的库直接用-l参数就能链接

# 链接glog库 g++ -lglog test.cpp

# 如果库文件没放在上面三个目录里，需要使用-L参数(大写)指定库文件所在目录
# -L参数跟着的是库文件所在的目录名

# 链接mytest库，libmytest.so在/home/bing/mytestlibfolder目录下
g++ -L/home/bing/mytestlibfolder -lmytest test.cpp
```

4.-I 指定头文件搜索目录

```bash
# -I 
# /usr/include目录一般是不用指定的，gcc知道去那里找，但 是如果头文件不在/usr/icnclude
里我们就要用-I参数指定了，比如头文件放在/myinclude目录里，那编译命令行就要加上-
I/myinclude 参数了，如果不加你会得到一个”xxxx.h: No such file or directory”的错
误。-I参数可以用相对路径，比如头文件在当前 目录，可以用-I.来指定。上面我们提到的–cflags参
数就是用来生成-I参数的。

g++ -I/myinclude test.cpp
```

5.-Wall 打印警告信息

```bash
# 打印出gcc提供的警告信息
g++ -Wall test.cpp
```

6.-w 关闭警告信息

```bash
# 关闭所有警告信息
g++ -w test.cpp
```

7.-std=c++11 设置编译标准

```bash
# 使用 c++11 标准编译 test.cpp
g++ -std=c++11 test.cpp
```

8.-o 指定输出文件名

```bash
# 指定即将产生的文件名

# 指定输出可执行文件名为test
g++ test.cpp -o test
```

9.-D 定义宏

```bash
# 在使用gcc/g++编译的时候定义宏

# 常用场景：
# -DDEBUG 定义DEBUG宏，可能文件中有DEBUG宏部分的相关信息，用个DDEBUG来选择开启或关闭
DEBUG
```

示例代码：

```bash
// -Dname 定义宏name,默认定义内容为字符串“1”

#include <stdio.h>

int main()
{
    #ifdef DEBUG
   printf("DEBUG LOG\n");
    #endif
   printf("in\n");
}

// 1. 在编译的时候，使用g++ -DDEBUG main.cpp
// 2. 第七行代码可以被执行
```

注：使用 man gcc 命令可以查看gcc英文使用手册

## g++命令行编译

demo代码：code/swap

```bash
# 最初目录结构

.
├── include
│   └── swap.h
├── main.cpp
└── src
   └── swap.cpp
    
2 directories, 3 files
```

### 直接编译

最简单的编译，并运行

```bash
# 将 main.cpp src/swap.cpp 编译为可执行文件
g++ main.cpp src/swap.cpp -Iinclude
# 运行a.out
./a.out
```

增加参数编译，并运行

```bash
# 将 main.cpp src/swap.cpp 编译为可执行文件 附带一堆参数
g++ main.cpp src/swap.cpp -Iinclude -std=c++11 -O2 -Wall -o b.out
# 运行 b.out
./b.out
```

### 生成库文件并编译

链接静态库生成可执行文件：

```bash
## 进入src目录下
cd src

# 汇编，生成Swap.o文件
g++ swap.cpp -c -I../include
# 生成静态库libSwap.a
ar rs libswap.a swap.o

## 回到上级目录
cd ..

# 链接，生成可执行文件:staticmain
g++ main.cpp -Iinclude -Lsrc -lswap -o staticmain
```

链接动态库生成可执行文件

```bash
## 进入src目录下
cd src

# 生成动态库libSwap.so
g++ swap.cpp -I../include -fPIC -shared -o libswap.so

## 上面命令等价于以下两条命令
# gcc swap.cpp -I../include -c -fPIC
# gcc -shared -o libswap.so swap.o

## 回到上级目录
cd ..

# 链接，生成可执行文件:sharemain
g++ main.cpp -Iinclude -Lsrc -lswap -o sharemain
```

运行可执行文件

```bash
# 静态编译的
./staticmain

# 动态编译的
LD_LIBRARY_PATH=src ./sharemain
```

# GDB调试器

前言：

- GDB(GNU Debugger)是一个用来调试C/C++程序的功能强大的调试器，是Linux系统开发

C/C++最常用的调试器

- 程序员可以使用GDB来跟踪程序中的错误，从而减少程序员的工作量。
- Linux 开发C/C++ 一定要熟悉 GDB
- VSCode是通过调用GDB调试器来实现C/C++的调试工作的；

GDB主要功能：

- 设置断点(断点可以是条件表达式)
- 使程序在指定的代码行上暂停执行，便于观察
- 单步执行程序，便于调试
- 查看程序中变量值的变化
- 动态改变程序的执行环境
- 分析崩溃程序产生的core文件

## 常用调试命令参数

调试开始：执行gdb [exefilename] ，进入gdb调试程序，其中exefilename为要调试的可执行文件名

```bash
## 以下命令后括号内为命令的简化使用，比如run（r），直接输入命令 r 就代表命令run
$(gdb)help(h) # 查看命令帮助，具体命令查询在gdb中输入help + 命令

$(gdb)run(r) # 重新开始运行文件（run-text：加载文本文件，run-bin：加载二进制文件）

$(gdb)start # 单步执行，运行程序，停在第一行执行语句

$(gdb)list(l) # 查看原代码（list-n,从第n行开始查看代码。list+ 函数名：查看具体函数）

$(gdb)set # 设置变量的值

$(gdb)next(n)   # 单步调试（逐过程，函数直接执行）

$(gdb)step(s) # 单步调试（逐语句：跳入自定义函数内部执行）

$(gdb)backtrace(bt) # 查看函数的调用的栈帧和层级关系

$(gdb)frame(f) # 切换函数的栈帧

$(gdb)info(i) # 查看函数内部局部变量的数值

$(gdb)finish # 结束当前函数，返回到函数调用点

$(gdb)continue(c) # 继续运行

$(gdb)print(p) # 打印值及地址

$(gdb)quit(q) # 退出gdb

$(gdb)break+num(b) # 在第num行设置断点

$(gdb)info breakpoints # 查看当前设置的所有断点

$(gdb)delete breakpoints num(d) # 删除第num个断点

$(gdb)display # 追踪查看具体变量值

$(gdb)undisplay # 取消追踪观察变量

$(gdb)watch # 被设置观察点的变量发生修改时，打印显示

$(gdb)i watch # 显示观察点

$(gdb)enable breakpoints # 启用断点

$(gdb)disable breakpoints # 禁用断点

$(gdb)x # 查看内存x/20xw 显示20个单元，16进制，4字节每单元

$(gdb)run argv[1] argv[2] # 调试时命令行传参

$(gdb)set follow-fork-mode child#Makefile项目管理：选择跟踪父子进程（fork()）
```

Tips:

> 编译程序时需要加上-g，之后才能用gdb进行调试：gcc -g main.c -o main
>
> 回车键：重复上一命令

## 命令行调试

demo代码：code/sum

```bash
# 编译
g++ -g sum.cpp -o sum
# 进入调试
gdb sum
```

# IDE - VSCode

## 插件安装

Linux下开发C/C++的三款必备插件

C/C++

CMake

CMake Tools

## 快捷键

| 功能              | 快捷键           | 功能          | 快捷键        |
| ----------------- | ---------------- | ------------- | ------------- |
| 转到文件/其他操作 | Ctrl + P         | 关闭当前文件  | Ctrl + W      |
| 打开命令面板      | Ctrl + Shift + P | 当前上移/下移 | Alt + Up/Down |
| 打开终端          | Ctrl + Shift + ` | 变量统一命名  | F2            |
| 关闭侧边栏        | Ctrl + B         | 转到定义处    | F12           |
| 复制文本          | Ctrl + C         | 粘贴文本      | Ctrl + V      |
| 保存文件          | Ctrl + S         | 撤销操作      | Ctrl + Z      |

在 Ctrl+P 窗口下还可以:

- 直接输入文件名，跳转到文件
- ? 列出当前可执行的动作
- ! 显示 Errors 或 Warnings ，也可以 Ctrl+Shift+M
- : 跳转到行数，也可以 Ctrl+G 直接进入
- @ 跳转到 symbol （搜索变量或者函数），也可以 Ctrl+Shift+O 直接进入
- @ 根据分类跳转 symbol ，查找属性或函数，也可以 Ctrl+Shift+O 后输入:进入
- `#`根据名字查找 symbol ，也可以 Ctrl+T

快捷键：编辑器与窗口管理

- 打开一个新窗口： Ctrl+Shift+N
- 关闭窗口： Ctrl+Shift+W
- 同时打开多个编辑器（查看多个文件）
- 新建文件 Ctrl+N
- 文件之间切换 Ctrl+Tab
- 切出一个新的编辑器（最多 3 个） Ctrl+\ ，也可以按住 Ctrl 鼠标点击 Explorer 里的文件名
- 左中右 3 个编辑器的快捷键 Ctrl+1 Ctrl+2 Ctrl+3
- 3 个编辑器之间循环切换 Ctrl+
- 编辑器换位置， Ctrl+k 然后按 Left 或 Right

快捷键：格式调整

- 代码行缩进 Ctrl+[ 、 Ctrl+]
- Ctrl+C 、 Ctrl+V 复制或剪切当前行/当前选中内容
- 代码格式化： Shift+Alt+F ，或 Ctrl+Shift+P 后输入 format code
- 上下移动一行： Alt+Up 或 Alt+Down
- 向上向下复制一行： Shift+Alt+Up 或 Shift+Alt+Down
- 在当前行下边插入一行 Ctrl+Enter
- 在当前行上方插入一行 Ctrl+Shift+Enter

快捷键：光标相关

- 移动到行首： Home
- 移动到行尾： End
- 移动到文件结尾： Ctrl+End
- 移动到文件开头： Ctrl+Home
- 移动到定义处： F12
- 定义处缩略图：只看一眼而不跳转过去 Alt+F12
- 移动到后半个括号： Ctrl+Shift+]
- 选择从光标到行尾： Shift+End
- 选择从行首到光标处： Shift+Home
- 删除光标右侧的所有字： Ctrl+Delete
- 扩展/缩小选取范围： Shift+Alt+Left 和 Shift+Alt+Right
- 多行编辑(列编辑)： Alt+Shift+鼠标左键 ， Ctrl+Alt+Down/Up
- 同时选中所有匹配： Ctrl+Shift+L
- Ctrl+D 下一个匹配的也被选中 (在 sublime 中是删除当前行，后面自定义快键键中，设置与Ctrl+Shift+K 互换了)
- 回退上一个光标操作： Ctrl+U

快捷键：重构代码

- 找到所有的引用： Shift+F12
- 同时修改本文件中所有匹配的： Ctrl+F12
- 重命名：比如要修改一个方法名，可以选中后按 F2 ，输入新的名字，回车，会发现所有的文件都修改了
- 跳转到下一个 Error 或 Warning ：当有多个错误时可以按 F8 逐个跳转
- 查看 diff ： 在 explorer 里选择文件右键 Set file to compare ，然后需要对比的文件上右键选择 Compare with file_name_you_chose

快捷键：查找替换

- 查找 Ctrl+F
- 查找替换 Ctrl+H
- 整个文件夹中查找 Ctrl+Shift+F

快捷键：显示相关

- 全屏： F11
- zoomIn/zoomOut： Ctrl +/-
- 侧边栏显/隐： Ctrl+B
- 显示资源管理器 Ctrl+Shift+E
- 显示搜索 Ctrl+Shift+F
- 显示 Git Ctrl+Shift+G
- 显示 Debug Ctrl+Shift+D
- 显示 Output Ctrl+Shift+U

# CMake
