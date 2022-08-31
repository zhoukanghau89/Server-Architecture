# Nginx学习

并发处理百万级别的TCP连接

高度模块化设计

自由许可证

热部署

反向代理



### 安装前提

1. Linux内核版本2.6以上
2. gcc、g++：编译器
3. pcre库：支持正则表达式
4. zlib库：压缩解压缩
5. openssl库：网站加密通讯



### 源码下载

http://nginx.org

Stable version：稳定版

安装： wget [nginx-1.20.2](http://nginx.org/download/nginx-1.20.2.tar.gz)

文件路径

```
/root/source_code/nginxsource/nginx-1.20.2
```

```c
[root@localhost nginx-1.20.2]# ll
总用量 792
drwxr-xr-x. 6 1001 1001   4096 5月  16 21:47 auto        // 编译相关脚本
-rw-r--r--. 1 1001 1001 312251 11月 16 22:44 CHANGES     // 修复的bug
-rw-r--r--. 1 1001 1001 476577 11月 16 22:44 CHANGES.ru  
drwxr-xr-x. 2 1001 1001    168 5月  16 21:47 conf        // 默认的配置文件
-rwxr-xr-x. 1 1001 1001   2590 11月 16 22:44 configure   // 可执行文件
drwxr-xr-x. 4 1001 1001     72 5月  16 21:47 contrib     // 脚本和工具
drwxr-xr-x. 2 1001 1001     40 5月  16 21:47 html        // 欢迎界面和错误界面的HTML文件
-rw-r--r--. 1 1001 1001   1397 11月 16 22:44 LICENSE
drwxr-xr-x. 2 1001 1001     21 5月  16 21:47 man
-rw-r--r--. 1 1001 1001     49 11月 16 22:44 README
drwxr-xr-x. 9 1001 1001     91 5月  16 21:47 src         // 源码目录
[root@localhost nginx-1.20.2]# d auto/
bash: d: 未找到命令...
[root@localhost nginx-1.20.2]# cd auto/
[root@localhost auto]# ls
cc          feature       headers  install  module   options  stubs    types
define      have          include  lib      modules  os       summary  unix
endianness  have_headers  init     make     nohave   sources  thread
```

源码目录

```c
[root@localhost src]# ll
总用量 20
drwxr-xr-x. 2 1001 1001 4096 5月  16 21:47 core     // 核心代码
drwxr-xr-x. 3 1001 1001 4096 5月  16 21:47 event    // 事件模块相关代码
drwxr-xr-x. 4 1001 1001 4096 5月  16 21:47 http     // web服务
drwxr-xr-x. 2 1001 1001 4096 5月  16 21:47 mail     // 邮件
drwxr-xr-x. 2 1001 1001   74 5月  16 21:47 misc     
drwxr-xr-x. 3 1001 1001   18 5月  16 21:47 os       // 操作系统
drwxr-xr-x. 2 1001 1001 4096 11月 16 22:44 stream   // l
```



### 编译和安装

编译之前的配置工作

默认安装的目录

```
--prefix     // 最终安装的目录 /usr/local/nginx
--sbin-path  // 指定可执行文件目录 sbin/nginx
--conf-path  // 指定配置文件目录 conf/nginx.conf
```

./configure 编译后生成mkaefile文件和objs文件

```
[root@localhost nginx-1.20.2]# cd objs/
[root@localhost objs]# ll
总用量 80
-rw-r--r--. 1 root root 17882 5月  18 15:54 autoconf.err
-rw-r--r--. 1 root root 40144 5月  18 15:54 Makefile
-rw-r--r--. 1 root root  6936 5月  18 15:54 ngx_auto_config.h
-rw-r--r--. 1 root root   657 5月  18 15:54 ngx_auto_headers.h
-rw-r--r--. 1 root root  5856 5月  18 15:54 ngx_modules.c        // 编译生成的中间文件
drwxr-xr-x. 9 root root    91 5月  18 15:54 src
```

make 编译

```apl
[root@localhost objs]# ll
总用量 3916
-rw-r--r--. 1 root root   17882 5月  18 15:54 autoconf.err
-rw-r--r--. 1 root root   40144 5月  18 15:54 Makefile
-rwxr-xr-x. 1 root root 3884048 5月  18 16:01 nginx               // nginx可执行文件
-rw-r--r--. 1 root root    5537 5月  18 16:01 nginx.8
-rw-r--r--. 1 root root    6936 5月  18 15:54 ngx_auto_config.h
-rw-r--r--. 1 root root     657 5月  18 15:54 ngx_auto_headers.h
-rw-r--r--. 1 root root    5856 5月  18 15:54 ngx_modules.c
-rw-r--r--. 1 root root   32696 5月  18 16:01 ngx_modules.o
drwxr-xr-x. 9 root root      91 5月  18 15:54 src
```

make 安装

```
make install
```

查看安装文件

```apl
[root@localhost local]# cd nginx/
[root@localhost nginx]# ls
conf  html  logs  sbin
[root@localhost nginx]# ll
总用量 4
drwxr-xr-x. 2 root root 4096 5月  18 16:10 conf  // 配置文件
drwxr-xr-x. 2 root root   40 5月  18 16:10 html  // 网页文件
drwxr-xr-x. 2 root root    6 5月  18 16:10 logs  
drwxr-xr-x. 2 root root   19 5月  18 16:10 sbin  // 可执行文件
[root@localhost nginx]# pwd
/usr/local/nginx
```



### 启动和运行

注意：nginx服务器监听的是80端口

```
[root@localhost sbin]# ./nginx 
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] still could not bind()
```

查看80端口使用情况

```
[root@localhost sbin]# netstat -lnp | grep 80
tcp6       0      0 :::80             :::*         LISTEN      1117/httpd  // 阿帕奇服务器
```

关闭80端口

关闭之前先关闭防火墙

```
systemctl status firewalld  // 检查防火墙状态
systemctl stop firewalld    // 关闭防火墙
```

关闭80端口

```
[root@localhost sbin]# fuser -k 80/tcp
80/tcp:               1117 122964 122965 122966 122967 122968
```

启动

```
[root@localhost sbin]# ./nginx 
```

查看运行状态

```apl
[root@bogon sbin]# ./nginx 
[root@localhost nginx]# ps -ef | grep nginx
//权限     进程ID  父进程    事件  TTY                 进程命令
root      20713      1  0 16:33 ?        00:00:00 nginx: master process ./nginx
nobody    20714  20713  0 16:33 ?        00:00:00 nginx: worker process
root      20756  12425  0 16:37 pts/8    00:00:00 grep --color=auto nginx
```

nginx是一个守护进程

使用 浏览器输入 192.168.245.128  端口号默认80可以不写

<img src="C:/Users/zhouk/AppData/Roaming/Typora/typora-user-images/image-20220518165252007.png" alt="image-20220518165252007" style="zoom: 67%;" />



常用命令

```
./nginx -s stop // 停止
./nginx -s quit // 安全退出
./nginx -s reload // 重新加载配置文件
```



常用概念

正向代理：在客户端配置代理服务器，通过代理服务器对互联网进行访问（代理客户端）

反向代理： 将请求发送到反向代理服务器，由反向代理服务器选择目标服务器获取数据，再返回给客户端（代理服务器端）

负载均衡：单个服务器解决不了问题，增加服务器的数量，将请求分发到各个服务器上，将原先多个请求集中到单个服务器上的情况改为分发到多个服务器上，将负载分发到不同的服务器

动静分离：为了加快网站的解析速度，将动态页面和静态页面由不同的服务器来解析，加快解析速度

[尚硅谷Nginx教程由浅入深（一套打通丨初学者也可掌握）](https://www.bilibili.com/video/BV1zJ411w7SV?p=8)



源码阅读

