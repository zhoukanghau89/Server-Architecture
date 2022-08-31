# bug调试

### 内核软锁死 `soft lockup`

`Message from syslogd@localhost at Mar 25 22:03:58 ... kernel:NMI watchdog: BUG:soft lockup - CPU#0 stuck for 22s!`

这个bug没有让系统彻底死机，但是若干个进程（或者kernel thread）被锁死在了某个状态（一般在内核区域），很多情况下这个是由于内核锁的使用的问题。

[](https://www.cnblogs.com/fusheng11711/p/10767190.html)

[](https://blog.csdn.net/a13568hki/article/details/104016751)

### 修改vim配置文件，子动换行对齐
