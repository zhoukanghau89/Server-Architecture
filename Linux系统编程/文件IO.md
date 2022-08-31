# 文件IO

对一个文件的访问，常由（ 用户访问权限和文件属性 ）共同限制。

文件是一系列的字节序列

它向应用程序提供了统一视角，即一切皆文件。对IO的打开操作相当于打开文件；IO的读操作相当于读文件；IO的写操作相当于写文件。

I/O种类：标准C库I/O，系统调用提供的I/O

标准C库 标准头文件 <stdio.h>

**文件描述符**：打开或者创建一个文件的时候，内核向进程返回一个文件描述符（非负整数），后续对文件的操作只需要通过该文件描述符（类似于指针的意思，这个文件描述符就是指向文件的）

一个进程最多可打开1024个文件

一个进程启动时，默认打开三个文件：

定义在<unistd.h>

标准输入（键盘）  0（STDIN_FILENO）

标准输出（屏幕）  1（STDOUT_FILENO）

标准错误（屏幕）   2（STDERR_FILENO）

默认操作：从STDIN_FILENO读取数据，向STDOUT_FILENO写数据，向STDERR_FILENO写错误

**输入输出重定向**：将原本指向屏幕的，从键盘读入的，改成自定义文件。在命令行中使用 >（输出）， <（输入） 

### 系统IO函数

#### open函数例程

```c
#include<unistd.h>
#include<sys/types.h>   //open
#include<sys/stat.h>   	//open
#include<fcntl.h>  		//open
#include<string.h>   	//strerror
#include<errno.h>  		//errno
#include<stdlib.h>
#include<stdio.h>
#include<ubistd.h> 		//close
/*
#define ERR_EXIT(m) (perror(m), exit(EXIT_FAILURE)) */
#define ERR_EXIT(m) \
         do \
         {  \
              perror(m); \
              exit(EXIT_FAILURE); \
         } while(0)
//void perror(const char* s) 打印error对应的错误描述
int main()
{
         int fd;
         fd = open ("test.txt", O_RDONLY);
         if (fd == -1) {
/*           fprintf(stderr, "open error with errno  = %d_%s\n", errno, strerror(errno));
             exit(EXIT_FAILURE);
*/

/*           perror("open error");
             exit(EXIT_FAILURE);
*/
            
             ERR_EXIT("open error");
         }
         printf("open succ\n");
         close (fd);
         return 0;

}

```

输出结果

<img src="C:\Users\zhouk\AppData\Roaming\Typora\typora-user-images\image-20220316205531505.png" alt="image-20220316205531505" style="zoom:80%;" />

errno全局变量记录错误信息，strerro()函数打印错误信息

也可以定义一个宏，一条语句，换行用”\“

打开的同时创建文件

int open(const char *pathname, int flags, mode_t mode)   //文件路径、打开方式、文件权限

```C
int main()
{
         int fd;
         fd = open ("test.txt", O_RDONLY | O_CREAT, 0666);
    //为什么是“|”，--按位或，32位存储方式，可读位|创建位，结果是可读且创建

         if (fd == -1) {
             ERR_EXIT("open error");
         }
         printf("open succ\n");
         return 0;

}

```

默认下umask值为022，数值取反结果为755，再与mode值想与得到644

<img src="C:\Users\zhouk\AppData\Roaming\Typora\typora-user-images\image-20220316212424634.png" alt="image-20220316212424634" style="zoom:80%;" />

也可以给umask重新设值为000，计算后可以保留mode值权限的文件      umask(0)；

<img src="C:\Users\zhouk\AppData\Roaming\Typora\typora-user-images\image-20220316212826507.png" alt="image-20220316212826507" style="zoom:80%;" />

size_t无符号整数

ssize_t有符号整数

#### write函数

```c
ssize_t write (int fd, const void * buf, size_t count); 
// 将buf指向的内存中的count个数据拷贝到文件描述符为fd的文件中【fd的接收缓冲区】
// 返回值：
// （1）阻塞模式
// nread = count 成功发送完毕
// count > fd接收缓冲区len，进程会被挂起，直到数据全部发送完毕write函数才会返回
// （2）非阻塞模式
// nread = count 成功发送完毕
// nread > 0 && nread < count 接收缓冲区已满，需要手动记录发送位置，buf += nread, count -= nread
// nread = -1 出错 errno = EAGAIN 发送缓冲区【或者fd的接收缓冲区】已满
```

#### send函数

```c
int send(int s, const void *msg, size_t len, int flags);
// flags = 0 与write功能一样
```

<img src="C:/Users/zhouk/AppData/Roaming/Typora/typora-user-images/image-20220622095935691.png" alt="image-20220622095935691" style="zoom: 50%;" />

#### read与write，拷贝文件例程

read 从文件中读取数据到内存

write 向文件中写数据

```c
ssize_t read(int fd, void *buf, size_t count);
//buf 指定读取的数据存放的地方
//count 存取数组的大小
//返回追 > 0 读取到的字节数； = 0 文件已经读完或者文件关闭；-1 失败
ssize_t read(int fd, void *buf, size_t count);
//将count字节数的buf中的数据写入到fd中 
```



```c
         int infd;
         int outfd;
         //grgc要有三个参数，./执行文件 源文件 目标文件
         if (argc != 3) {
               fprintf(stderr, "USage %s strc dest\n", argv[0]);
               exit(EXIT_FAILURE);
         }
         infd = open(argv[1], O_RDONLY);
         if (infd == -1)  
                ERR_EXIT("open src error");
         outfd = open (argv[2], O_WRONLY | O_CREAT | O_TRUNC, 0666);
         if (outfd == -1)
                ERR_EXIT("open dest error");
         //定义一个缓冲区
         char buf[1024];
         int nread;
         while ((nread = read(infd, buf, 1024)) > 0) {
              write(outfd, buf, nread);
         }
         close(infd);
         close(outfd);

```

拷贝test.txt文件，运行结果：

```c
[root@localhost IO]#./03copy test.txt test1.txt
[root@localhost IO]# ls
01open  01open.c  02open  02open.c  03copy  03copy.c  test1.txt  test.txt
[root@localhost IO]# cat test1.txt
1111
2222
```

#### 获取文件数据信息stat

```c
[root@localhost IO]# stat 03copy.c
  文件："03copy.c"
  大小：1008      	块：8          IO 块：4096   普通文件
设备：fd00h/64768d	Inode：51418596    硬链接：1
权限：(0644/-rw-r--r--)  Uid：(    0/    root)   Gid：(    0/    root)
环境：unconfined_u:object_r:admin_home_t:s0
最近访问：2022-03-17 20:07:41.027739745 +0800
最近更改：2022-03-17 20:07:32.562753571 +0800
最近改动：2022-03-17 20:07:32.562753571 +0800
创建时间：-
```

#### 文件共享

文件描述表 前三位是标准输入、标准输出、标准错误

一个进程两次打开同一个文件，两次的文件偏移量不会相互影响

复制文件描述符可以达到IO重定向

```c
        int fd;
         fd =  open("test.txt", O_WRONLY);
         if (fd == -1) {
              ERR_EXIT("open error");
         }
         close(1);   //关闭标准输出
         dup(fd);    //复制文件描述符
         printf("hello\n");  //将hello输出到test.txt文件中
         close(fd);

[root@localhost IO]# cat test.txt
hello
222
3333
```

#### fcntl函数

主要两个功能：

1.复制文件描述符

2.修改或获取文件描述符的状态`flag`

```c
//int fcntl(int fd, int cmd, ... /* arg */ );
/*
cmd：
     F_DUPFD 复制文件描述符
     F_GETFL 获取fd的文件描述符状态
     F_SETFL 设置fd的文件描述符状态
             必须项 O_RDONLY O_WRONLY O_RDWR 不可被修改
             可选项 O_APPEND  表示追加数据
                    O_NONBLOCK 设置非阻塞
*/
int main(int argc, char *argv[])
{
     int fd;
     fd = open("test.txt", O_RDWR);
     if (fd == -1)  ERR_EXIT("open error");
     printf("fd: %d\n", fd);
     
     int fd1 = fcntl(fd, F_DUPFD, 0);
     if (fd1 == -1) ERR_EXIT("fcntl");
     printf("fd1: %d\n", fd1);
     
     int flag = fcntl(fd1, F_GETFL);
     int ret = fcntl(fd1, F_SETFL, flag | O_APPEND);
     char* str = "aaaa"; 
     write(fd1, str, strlen(str));
     close(fd);
     close(fd1);
     return 0;
}
[root@localhost IO]# ./05fcntl
fd: 3
fd1: 4
[root@localhost IO]# vim test.txt
[root@localhost IO]# cat test.txt
hello
aaaaaaaa
```

#### 复制文件描述符 dup函数

从空闲的文件描述符表中寻找一个最小的可用的。

```c
int main()
{
         int fd;
         fd = open ("test1.txt", O_WRONLY);

         if (fd == -1) {       
             ERR_EXIT("open error");      
         }

         int fd1 = dup(fd);
         if (fd1 == -1) ERR_EXIT("dup");
         printf("open succ\n");
         printf("fd: %d, fd1: %d\n", fd, fd1);
         close(fd);
         char *str = "hello world";
         int ret = write(fd1, str, strlen(str));
         if(ret == -1) ERR_EXIT("write");
         close(fd1);
         return 0;
}
[root@localhost IO]# ./06dup   
open succ
fd: 3, fd1: 4 
```

#### 重定向文件描述符 dup2函数

`fd1 指向文件1，fd2指向文件2`

`int fd3 = dup2(fd1, fd2)`

`fd3指向文件2，fd2指向文件1`



### 标准IO库函数

不会直接写到内核缓冲区

应用缓冲区与内核缓冲区之间还存在一个CLIB缓冲区

标准库IO函数会写入到CLIB缓冲区

底层也会调用系统IO函数



### 目录、文件权限

**-：普通文件，d:目录文件，l:链接文件，b:设备文件，c:字符设备文件，p:管道文件**

目录的最大默认权限777：rwxrwxrwx

文件的最大默认权限666：rw-rw-rw-

查看用户的权限：umask命令

修改用户的权限掩码：umask xxx命令

实际权限 + 权限掩码 = 默认权限

在一个空目录下执行umask 333; touch hello;命令后，hello文件的权限为？

目录的默认权限是777：111111111

权限掩码333：011011011

得出实际权限：100100100 ->444





