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
