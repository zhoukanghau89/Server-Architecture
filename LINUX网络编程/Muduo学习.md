# Muduo学习

安装依赖

cmake、boost-devel

版本 

muduo-0.9.1-beta.tar.gz

解压缩后

./build.sh

```c
[root@localhost muduo]# ll
总用量 156
drwxr-xr-x. 3 root root     19 5月  20 20:49 build
drwxr-xr-x. 4 zhou zhou    225 5月  20 20:57 muduo
-rw-r--r--. 1 root root 158662 5月  20 20:47 muduo-0.9.1-beta.tar.gz

[root@localhost debug]# ll
总用量 1084
drwxr-xr-x.  2 root root  4096 5月  20 20:58 bin        // 测试程序
drwxr-xr-x. 23 root root  4096 5月  20 20:57 examples
drwxr-xr-x.  2 root root   125 5月  20 20:57 lib        // 静态库
-rw-r--r--.  1 root root 68956 5月  20 20:57 Makefile
drwxr-xr-x.  4 root root    29 5月  20 20:49 muduo

/*
    bin/目录下是测试程序，可以理解为任务服务器，运行后，可以获取应用服务器的一些功能
    比如inspector_test是一个返回服务信息的一个服务
    运行后，浏览器输入http://192.168.245.128:12345/proc/status
    可以获取当前服务器的z
*/
    
[root@localhost muduo]# ll
总用量 8
drwxr-xr-x. 3 zhou zhou 4096 1月  17 2013 base   // 基础库
drwxr-xr-x. 7 zhou zhou 4096 1月  17 2013 net    // 网络库
[root@localhost muduo]# pwd
/root/source_code/muduo/muduo/muduo
```

在muduo目录下执行./build.sh install，创建一个目录build/debug-install， 里面有库的头文件和库文件

```
[root@localhost debug-install]# ll
总用量 0
drwxr-xr-x. 3 root root  19 5月  20 21:57 include   // 头文件
drwxr-xr-x. 2 root root 100 5月  20 21:57 lib       // k
```



C++基于对象编程风格
