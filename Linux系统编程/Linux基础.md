# Linux基础

#### gcc、g++运行流程

`.h、.c、.cpp`源代码

`.i`预处理后源代码 `-E`

`.s`汇编代码 `-S`

`.o`目标代码，库代码（二进制文件）`-o`

`.exe、.out`可执行程序

`-c` 编译、汇编

编译用`gcc/g++`，连接用`g++ 或 gcc -lstdc++`

库文件：静态库、动态库

命名：`libxxx.a`

#### Makefile

生成执行文件，不需要每次`gcc`编译，只需`make`

```makefile
-c 源文件
-o m
目标文件：依赖文件
[tab]   命令

变量 = (替换) += (追加) := (恒等于)
     使用变量 $(变量名)
隐含规则 %.c %.o 任意的.或者.o
       *.c *.o 所有的.或者.o
通配符  $@ 所有的目标文件
       $^ 所有的依赖文件
       $< 第一个依赖文件
”\“连接到下一行
```



```cpp
app:14epoll-src.cpp
    g++ 14epoll-src.cpp -o app
[root@localhost 14socket]# make
```

```c++
app:conntest.o
    gcc conntest.o -o app
conntest.o:conntest.c
    gcc -c conntest.c -o conntest.o
[root@localhost 14socket]# make
gcc -c conntest.c -o conntest.o
gcc conntest.o -o app
[root@localhost 14socket]# ls
14epoll-src  14epoll-src.cpp  app  conntest  conntest.c  conntest.o  Makefile
[root@localhost 14socket]# ./app
connect: Connection refused
```

#### GDB调试

调试，暴露程序问题出在哪！

默认调试主进程，7.0版本支持调试子进程

```shell
编译选项： -g
编译：gdb 执行程序
查看进程：show follow-fork-mode
设置子进程：set follow-fork-mode child
只调试一个进程：detach-on-fork o
```

```shell
[root@localhost net-programming]# gcc test.c -o test1 -g
[root@localhost net-programming]# gdb test1
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-100.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /root/net-programming/test1...done.
(gdb) list
1	#include <stdio.h> 
2	int main() {
3	    printf("hello word\n");
4	    printf("hello word\n");
5	    return 0;
6	
7	}(gdb) 
Line number 8 out of range; test.c has 7 lines.
(gdb) quit
```

#### 标准C库函数IO函数、Linux系统IO函数

输入输出要看站在哪个角度。

一般在内存的角度，输入是从文件读取数据到内存中；输出是从内存向文件中写入数据。

标准C库函数：

```cpp
fopen, fclose, fgets, fputs, fprintf, fread, fwrite
```

标准C库函数在不同平台下，使用不同平台的API

Linux系统IO函数：适用于网络通信，每写一次数据就调用一次磁盘

在Linux平台下，调用fopen时，会先调用Linux底层API的open函数

标准C库函数带缓冲区

数据从内存到磁盘：1.刷新缓冲区 2.缓冲区已满 3.正常关闭文件 a.fclose b.return c.exit

![image-20220404205906170](C:\Users\zhouk\AppData\Roaming\Typora\typora-user-images\image-20220404205906170.png)

![image-20220404205713479](C:\Users\zhouk\AppData\Roaming\Typora\typora-user-images\image-20220404205713479.png)

#### 虚拟地址空间

![image-20220404212133222](C:\Users\zhouk\AppData\Roaming\Typora\typora-user-images\image-20220404212133222.png)



#### 常用命令

```shell
#查看网络信息
netstat -anp | grep -E 'State | port'
#查看地址命令
man 7 ip
#查看进程
ps -aux | grep process 
ps -ef | grep process
#显示具体信息
ps -eo pid,ppid,sid,tty,pgrp,comm,stat,cmd | grep -E 'PID|webserver'
#查看80端口使用q
netstat -lnp | grep 80 
#关闭80端口
fuser -k 80/tcp
#查看80端口被哪些进程
lsof -i:80
#查看每个物理CPU的核数
cat /proc/cpuinfo| grep "cpu cores"| uniq
```

