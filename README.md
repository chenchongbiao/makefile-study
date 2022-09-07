# 编译过程

Makefile->Maketools(compile)->SourceFile

Makefile文件中的命令有一定规范，一旦该文件编写好以后在Linux命令行中执行一条make命令即可自动编译整个工程。不同厂家的make可能会稍有不同，并且语法上也有区别，不过基本思想都差不多，主要还是落在目标依赖上，最广泛使用的是GNUmake。

# 语法规则

```makefile
目标 ... : 依赖 ...
	命令1
	命令2
	. . .

```

1、目标即要生成的文件。如果目标文件的更新时间晚于依赖文件更新时间，则说明依赖文件没有改动，目标文件不需要重新编译。否则会进行重新编译并更新目标文件。
2、默认情况下Makefile的第一个目标为终极目标。
3、依赖：即目标文件由哪些文件生成。
4、命令：即通过执行命令由依赖文件生成目标文件。注意每条命令之前必须有一个tab保持缩进，这是语法要求（会有一些编辑工具默认tab为4个空格，会造成Makefile语法错误）。
5、all：Makefile文件默认只生成第一个目标文件即完成编译，但是我们可以通过all 指定所需要生成的目标文件。

## 变量

$符号表示取变量的值，当变量名多于一个字符时，使用"( )"
$符的其他用法

```makefile
$^ 表示所有的依赖文件
$@ 表示生成的目标文件
$< 代表第一个依赖文件
```

```makefile
SRC = $(wildcard *.c)
OBJ = $(patsubst %.c, %.o, $(SRC))
 
ALL: hello.out
 
hello.out: $(OBJ)
        gcc $< -o $@
 
$(OBJ): $(SRC)
        gcc -c $< -o $@
```

## 变量赋值

"="是最普通的等号，在Makefile中容易搞错赋值等号，使用 "="进行赋值，变量的值是整个Makefile中最后被指定的值。

```makefile
VIR_A = A
VIR_B = $(VIR_A) B
VIR_A = AA
```

经过上面的赋值后，最后VIR_B的值是AA B，而不是A B，在make时，会把整个Makefile展开，来决定变量的值

":=" 表示直接赋值，赋予当前位置的值。

```makefile
VIR_A := A
VIR_B := $(VIR_A) B
VIR_A := AA
```

最后BIR_B的值是A B，即根据当前位置进行赋值。因此相比于"="，":="才是真正意义上的直接赋值

"?=" 表示如果该变量没有被赋值，赋值予等号后面的值。

```makefile
VIR ?= new_value
```

如果VIR在之前没有被赋值，那么VIR的值就为new_value。

```makefile
VIR := old_value
VIR ?= new_value
```

这种情况下，VIR的值就是old_value
"+="和平时写代码的理解是一样的，表示将符号后面的值添加到前面的变量上。

## 预定义变量

CC：c编译器的名称，默认值为cc。cpp c预编译器的名称默认值为$(CC) -E

```makefile
CC = gcc
```

回显问题，Makefile中的命令都会被打印出来。如果不想打印命令部分 可以使用@去除回显。

```makefile
@echo "clean done!"
```

## 函数

通配符

```makefile
SRC = $(wildcard ./*.c)
```

匹配目录下所有.c 文件，并将其赋值给SRC变量。

```makefile
OBJ = $(patsubst %.c, %.o, $(SRC))
```

这个函数有三个参数，意思是取出SRC中的所有值，然后将.c 替换为.o 最后赋值给OBJ变量。
示例：如果目录下有很多个.c 源文件，就不需要写很多条规则语句了，而是可以像下面这样写

```makefile
SRC = $(wildcard *.c)
OBJ = $(patsubst %.c, %.o, $(SRC))
 
ALL: hello.out
 
hello.out: $(OBJ)
        gcc $(OBJ) -o hello.out
 
$(OBJ): $(SRC)
        gcc -c $(SRC) -o $(OBJ)
```

这里先将所有.c 文件编译为 .o 文件，这样后面更改某个 .c 文件时，其他的 .c 文件将不在编译，而只是编译有更改的 .c 文件，可以大大提高大项目中的编译速度。

## 伪目标 .PHONY

伪目标只是一个标签，clean是个伪目标没有依赖文件，只有用make来调用时才会执行
当目录下有与make 命令 同名的文件时 执行make 命令就会出现错误。
解决办法就是使用伪目标

```makefile
SRC = $(wildcard *.c)
OBJ = $(patsubst %.c, %.o, $(SRC))
 
ALL: hello.out
 
hello.out: $(OBJ)
        gcc $< -o $@
 
$(OBJ): $(SRC)
        gcc -c $< -o $@
 
clean:
        rm -rf $(OBJ) hello.out
 
.PHONY: clean ALL
```

通常也会把ALL设置成伪目标

## 嵌套执行Makefile

在一些大工程中，会把不同模块或不同功能的源文件放在不同的目录中，我们可以在每个目录中都写一个该目录的Makefile这有利于让我们的Makefile变的更加简洁，不至于把所有东西全部写在一个Makefile中。
  列如在子目录subdir目录下有个Makefile文件，来指明这个目录下文件的编译规则。外部总Makefile可以这样写

```makefile
subsystem:
            cd subdir && $(MAKE)
其等价于：
subsystem:
            $(MAKE) -C subdir
```

定义$(MAKE)宏变量的意思是，也许我们的make需要一些参数，所以定义成一个变量比较有利于维护。两个例子意思都是先进入"subdir"目录，然后执行make命令
  我们把这个Makefile叫做总控Makefile，总控Makefile的变量可以传递到下级的Makefile中，但是不会覆盖下层Makefile中所定义的变量，除非指定了 "-e"参数。
  如果传递变量到下级Makefile中，那么可以使用这样的声明
  export
  如果不想让某些变量传递到下级Makefile，可以使用
  unexport

```go
export variable = value
等价于
variable = value
export variable
等价于
export variable := value
等价于
variable := value
export variable
如果需要传递所有变量，那么只要一个export就行了。后面什么也不用跟，表示传递所有变量
```

## 指定头文件路径

一般都是通过"-I"（大写i）来指定，假设头文件在：

```bash
/home/develop/include
```

则可以通过-I指定：

```bash
-I/home/develop/include
```

将该目录添加到头文件搜索路径中
在Makefile中则可以这样写：

```bash
CFLAGS=-I/home/develop/include
```
