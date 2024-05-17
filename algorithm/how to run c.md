# gcc

https://www.bilibili.com/video/BV1rJ411V7EV?p=1

## 参数

- -E ：预编译，替换# 宏定义等
- -D ：添加预处理参数
- -S ：编译
- -c ：汇编，只编译 不链接为可执行文件
- -o <输出文件名> ： 指定编译结束后指定的文件名
- -I ：指定头文件路径
  - gcc  -Wall -Iinclude  -c  main.c 
- -L ：指定库文件路径
  - gcc  -Wall  main.c  -Llib  -lhello  -o  main
- -l ：指定库文件  --xx.a
  - gcc  -Wall  main.c  -Llib  -lhello  -o  main
  - -lhello ：./lib/libhello.a
- -std ：明确指定gcc使用的语言标准
  - -ansi
  - -pedantic
- -Wall ：输出警告信息
  - -Wcomment
  - -Wformat
  - -Wunused
  - -Wimplicit
  - -Wreturn-type
  - 未被包含的
    - -W
    - -Wconversion
    - -Wshadow
    - -Wcast-qual
    - -Wwrite-strings
    - -Wtraditional
- -g ：添加调试信息，需要配合调试工具
- -O ：优化编译
- -O2 ：加大幅度的优化编译
- 链接：gcc main.o -o main 
- 多文件编译：gcc func.c main.c -o main 、gcc ./* -o main

## .h .c .a .so

- .h ：头文件，#include 引入，在预编译过程中 c文件会替换头文件
  - #include <stdio.h> ：从标准库、path路径开始寻找
  - #include "stdio.h" ：从当前路径开始寻找
  - 系统缺省头文件，gcc默认从以下路径寻找
    - /usr/local/include/
    - /usr/local/
- .c ：源文件
- .a ：静态库文件，见ar，直接拷贝机器码 进程序，**会多占内存**
  - 如：gcc -Wall main.c /usr/lib/libm.a -o main
  - 可简写：gcc -Wall main.c -lm -o main
  - 系统缺省库文件，gcc默认从以下路径寻找
    - /usr/local/lib
    - /usr/lib
- .so ：共享库文件，不拷贝机器码进程序，而是拷贝内存地址
- .o ：编译完成 等待链接的文件

## ar，创建静态库

ar  cr  libNAME.a  file1.o  file2.o 。。。 

ar  t  libNAME.a  列出.a 库文件的链接文件.o，如：ar t /usr.lib/libz.a

gcc  -Wall  main.c  libhello.a  -o  main ，使用自己创建的库文件，注意顺序

## GNU 方言

default 一个c语言标准，linux内核使用此标准

## 预处理 #

实质就是字符串替换

可使用 -E 查看预处理结果

```c
#include <stdio.h> // 引入，拷贝
int main()
{
// 若为false 则此if包含的代码将被删除，可做多层注释
#if 0
	printf("test1");
#endif
    printf("test1");
    
	int i;
#define NUM (1024 + 123)
#define NUM1 1024 + 123
	i = NUM * 2; // (1024 + 123) * 2
	i = NUM1 * 2; // 1024 + 123 * 2
    
// 此处TEST 可用 “gcc -Wall -DTEST main.c -o main” 传递参数
#ifdef TEST 
    printf("TEST is define\n");
# endif

// 此处NN 可用 “gcc -Wall -DNN=ttt main.c -o main” 传递参数
    printf("NN is %s\n", NN);
    
// 此处NN 可用 “gcc -Wall -DCC="1+2" main.c -o main” 传递参数
    printf("CC is %d\n", CC);
    return 0;
}
```

**查看cpp 默认宏定义：** cpp -dM /dev/null

## 调试 -g

1. gcc -Wall -g main.c -o main
2. ./main 报错 Segmentation fault
3. uimit -c 
   1. 输出 0 ：不允许 core dumped
   2. 输出unlimited ：允许产生core dumped调试文件
4. ulimit -c unlimited ，开启允许产生core dumped文件
5. ./main 报错 Segmentation fault (core dumped)
6. 产生 core.2297 文件
7. gdb main core.2297
   1. print + 参数名
   2. backreace 打印调用关系
   3. quit

## 性能优化

一般发生在源码级别

### 1. CSE (Common Subexpression Elimination 子表达式消除)

重用 已经计算过的结果

```c
x = cos(v) * (1 + sin(u / 2)) + sin(w) + (1 - sin(u / 2))
优化为 ===>
t = sin(u / 2);
x = cos(v) * (1 + t) + sin(w) + (1 - t)
```

### 2. FL (Function Inlining 函数内联)

函数调用 通过汇编了解到，是一个频繁压栈、出栈的过程。

```
double sq(double x)
{
	return x * x;
}
double sum = 0.0;
for(int i = 0; i < 100_0000; i++)
{
	sum += sq(i + 0.5);
}
// 以下改造可减少压栈、出栈过程，提高代码执行速度
// 优化为 ===>
for(int i = 0; i < 100_0000; i++)
{
	t = i + 0.5;
	sum += t * t;;
}
```

### 3. Loop Unrolling 循环优化

是一个空间 时间权衡的优化方式 (speed-space tradeoff)

```
for(int i = 0; i < 7; i++)
{
	y[i] = i;
}
// 通过减少if 判断来提升性能
// 优化为 ===>
y[0] = 0;
y[1] = 1;
y[2] = 2;
y[3] = 3;
y[4] = 4;
y[5] = 5;
y[6] = 6;
```

```
for(int i = 0; i < n; i++)
{
	y[i] = i;
}
// 优化为 ===>
for(int i = 0; i < (n % 2); i++)
{
	y[i] = i;
}
for(int i = 0; i+1 < n; i+=2)
{
	y[i] = i;
	y[i+1] = i+1;
}
```







# make Makefile

https://blog.csdn.net/LEON1741/article/details/78174953

http://www.gnu.org/software/make/manual/

源代码管理工具，使用gcc进行编译，自动编译工具

**注：每个平台的Makefile格式不一样**

## Makefile 规则格式

文件名：Makefile

```
目标: 依赖文件集合。。。
	命令1
	命令2
	。。。
目标2: 依赖文件集合
	命令1
	命令2
	。。。
```

注：这里写命令的地方用tab键缩进，终极目标放在第一个

**Makefile文件编写完成后，在Makefile同级目录执行make命令，就会按照Makefile文件进行执行**

例1：

1. 目录下有main.c func.h func.c，main.c 引入 func.h

2. 编写Makefile

   ```
   main: main.o func.o
   	gcc -o main main.o func.o
   main.o: main.c
   	gcc -c main.c
   func.o: func.c
   	gcc -c func.c
   	
   clean:
   	rm *.o
   	rm main
   ```

3. 执行make命令，根据当前目录的Makefile文件编译执行

4. 执行 ./main 即可执行程序

5. 执行 make clean 命令可清除编译 链接后的文件

## Makefile 变量

Makefile的变量都是字符串

如例1第一个目标 可改写成

```
objs = main.o func.o 
main: $(objs)
	gcc -o main $(objs)
```

## Makefile 赋值

| 操作符 | 功能                     |
| ------ | ------------------------ |
| =      | 类似于引用，a = $(xxx)。 |
| :=     | 直接赋值                 |
| ?=     | 如果有值 则不赋值        |
| +=     | 追加                     |

## Makefile 模式规则

% ，类似于通配符

如 例1 中第二个规则可改写成

```
%.o: %.c
	gcc -c main.c
```

通常配合自动化变量使用

## Makefile 自动化变量

| 自动化变量 | 描述                                                         | 简介             |
| :--------: | ------------------------------------------------------------ | ---------------- |
|   **$@**   | 规则中的目标集合,在模式规则中,如果有多个目标的话,“$@”表示匹配模式中定义的目标集合。 | 目标全           |
|     $%     | 当目标是函数库的时候表示规则中的目标成员名,如果目标不是函数库文件,那么其值为空。 |                  |
|   **$<**   | 依赖文件集合中的第一个文件,如果依赖文件是以模式(即“%”)定义的,那么“$<”就是符合模式的一系列的文件集合。 | 第一个依赖       |
|     $?     | 所有比目标新的依赖目标集合,以空格分开                        | 第一个变化的依赖 |
|   **$^**   | 所有依赖文件的集合,使用空格分开,如果在依赖文件中有多个重复的文件,“$^”会去除重复的依赖文件,值保留一份 | 全部依赖         |
|     $+     | 和“$^”类似,但是当依赖文件存在重复的话不会去除重复的依赖文件。 |                  |
|   **$***   | 这个变量表示目标模式中"%"的部分,如果目标是test/a.test.c,目标模式为a.%.c,那么“$*”就是 test/a.test | 目标中的 %       |

则 例1 可改写成：

```
main: main.o func.o
	gcc -o main main.o func.o
%.o: %.c
	gcc -c $<
	
clean:
	rm *.o
	rm main
```

## Makefile 伪目标

如 例1 中的 clean目标， 如果有一个名为clean的文件，执行make clean 就 不会执行clean下的命令 rm 等

声明为伪目标 可避免这种情况

则 例1 可改写为

```
.PHONY: clean
main: main.o func.o
	gcc -o main main.o func.o
main.o: main.c
	gcc -c main.c
func.o: func.c
	gcc -c func.c
	
clean:
	rm *.o
	rm main
```

## Makefile @

命令前加@ 控制台就不打印执行的命令

如 例1 改写为

```
main: main.o func.o
	@gcc -o main main.o func.o
main.o: main.c
	@gcc -c main.c
func.o: func.c
	@gcc -c func.c
	
clean:
	@rm *.o
	@rm main
```

## Makefile -

命令前加 “-”，则命令报错仍继续执行后续命令

如 例1 改写，执行make clean

```
main: main.o func.o
	gcc -o main main.o func.o
main.o: main.c
	gcc -c main.c
func.o: func.c
	gcc -c func.c
	
clean:
	-rm *.o
	-@rm *.o
	rm main
```

## Makefile 函数

1. **wildcard** 找到所有文件
2. **patsubst** 替换

则 例1 改写为

```
srcs = $(wildcard *.c)
objs = $(patsubst %.c,%.o,$(srcs))

main: $(objs)
	gcc -o main $(objs)
	
%.o: %.c
	gcc -c $< -o $@
	
clean:
	rm -f *.o
	rm -f main
```

## Makefile 伪目标 all

一般定义 all  是为了生成多个目标

则 例1 改写为

```
all: main main1

main: main.o func.o
	gcc -o $@ main.o func.o
main1: main.o func.o
	gcc -o $@ main.o func.o
main.o: main.c
	gcc -c main.c
func.o: func.c
	gcc -c func.c
	
clean:
	rm *.o
	rm main main1
```

## 保留中间文件

```
# 保留中间文件
.PRECIOUS: %.o
# 保留所有中间文件
.SECONDARY:
```



# CMake

自动根据平台生成make 的 Makefile文件

https://www.bilibili.com/video/BV16V411k7eF?from=search&seid=11581367427860258173&spm_id_from=333.337.0.0

语法有点复杂，先弃用

## 语法

文件名：CMakeLists.txt

```
```













# 相关学习

B站搜 正点原子

