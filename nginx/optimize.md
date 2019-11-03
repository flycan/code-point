# Nginx 优化

## 优化方法论

从软件层面提升硬件使用效率

    -增大CPU的利用率 
    -增大内存的利用率 
    -增大磁盘10的利用率 
    -增大网络带宽的利用率

提升硬件

    -网卡：万兆网卡，例如10G、25G、40G等 
    -磁盘:固态硬盘 
    -CPU :更快的主频，更多的核心，更大的缓存
    -内存：更快的访问速度


### 增加Nginx使用CPU的有效时长

能够使用全部CPU资源

    master-worker多进程架构 
    worker进程数量应当大于等于CPU核数

 Nginx进程间不做无用功浪费CPU资源

    . worker进程不应在繁忙时，主动让出CPU   
    . worker进程间不应由于争抢造成资源耗散      
    . worker进程数量应当等于CPU核数      
    . worker进程不应调用一些API导致主动让出CPU   
    . 比如使用openresty调用第三方模块

不被其他进程争抢资源

    . 提升优先级，占用CPU更长的时间     
    . 减少操作系统上耗资源的其他进程

> 设置worker进程的数量

    worker processes number auto; 

> 减入进程间切换

    减少被动切换（时间片耗尽）
    
    增大worker进程的时间片，通过设置优先级赖减入被动切换
    什么决定CPU时间片的大小?   在默认情况下，最小时间片是5ms，最大则有800ms   进程分配的CPU时间片大小也与优先级有关，优先级越高，时间片越长      
    
    Nice静态优先级：-20 - 19 静态优先级越小越不友好则时间片越长，可以通过top程序看到
    
    设置worker进程的静态优先级
    Syntax: worker_priority number; 
    Default: worker_priority 0; 
    Context: main

> 减入被动进程间切换

    nginx默认是没有开启利用多核cpu的配置的

    增加worker_cpu_affinity配置参数来充分利用多核cpu

> 最大连接数

    worker_connections number; 

> 最大打开文件数

    worker_rlimit_nofile  number; 

    file-max是内核可分配的最大文件数可通过 cat /proc/sys/fs/file-max 查看
    nr_open是单个进程可分配的最大文件数 可通过 cat /proc/sys/fs/nr_open 查看

> 修改用户的最大文件句柄数限制 

    临时修改 ulimit -HSn limit
    永久修改 vi /etc/security/limits.conf

    -H选项和-S选项分别表示对给定资源的硬限制（hard limit）和软限制（soft limit）进行设置。
    
    硬限制（hard limit）一旦被设置以后就不能被非root用户修改，软限制（soft limit）可以增长达到硬限制（hard limit）。
    
    如果既没有指定-H选项也没有指定-S选项，那么硬限制（hard limit）和软限制（soft limit）都会被设置。



### 进程打开的文件数

    - 所有进程打开的文件描述符数不能超过/proc/sys/fs/file-max  
    - 单个进程打开的文件描述符数不能超过user limit中- nofile的soft limit  
    - nofile的soft limit不能超过其hard limit  
    - nofile的hard limit不能超过/proc/sys/fs/nr_open

> 信号

SIGHUP 终止进程 终端线路挂断
SIGINT 终止进程 中断进程
SIGIOT 建立CORE文件 执行I/O自陷
SIGKILL 终止进程 杀死进程
SIGPIPE 终止进程 向一个没有读进程的管道写数据
SIGALARM 终止进程 计时器到时
SIGTERM 终止进程 软件终止信号
SIGSTOP 停止进程 非终端来的停止信号
SIGTSTP 停止进程 终端来的停止信号
SIGCONT 忽略信号 继续执行一个停止的进程
SIGURG 忽略信号 I/O紧急信号
SIGIO 忽略信号 描述符上可以进行I/O
SIGPROF 终止进程 统计分布图用计时器到时
SIGUSR1 终止进程 用户定义信号1
SIGUSR2 终止进程 用户定义信号2
SIGVTALRM 终止进程 虚拟计时器到时