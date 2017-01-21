title: Nginx 安装 启动 停止 重启
date: 2015-01-03 07:50:11
tags:
- nginx
categories:
- nginx
---

<blockquote class="blockquote-center">
Nigix 安装 使用 配置
</blockquote>
<!--more-->
## 0 安装

```sh
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:nginx/stable
sudo apt-get update
sudo apt-get install nginx

```
## 2 启动,停止,重启

```
nginx -s signal

```
signal 的内容如下
```
stop — 快速停止
quit — 优雅的停止
reload — 重启
reopen — 重新打开日志文件

```

## 3 关于配置文件

#### 2.1 配置文件简单说明
配置文件可以看做是Nginx的灵魂，Nginx服务在启动时会读入配置文件，而后续几乎一切动作行为都是按照配置文件中的指令进行的，因此如果将Nginx本身看做一个计算机，那么Nginx的配置文件可以看成是全部的程序指令。

Nginx配置文件是纯文本文件，你可以用任何文本编辑器如vim或emacs打开它，通常它会在nginx安装目录的conf下

其中“#”表示此行是注释，由于笔者为了学习扩展开发安装了一个纯净的Nginx，因此配置文件没有经过太多改动。

Nginx的配置文件是以block的形式组织的，一个block通常使用大括号“{}”表示。block分为几个层级，整个配置文件为main层级，这是最大的层级；在main层级下可以有event、http等层级，而http中又会有server block，server block中可以包含location block。

每个层级可以有自己的指令（Directive），例如worker_processes是一个main层级指令，它指定Nginx服务的Worker进程数量。有的指令只能在一个层级中配置，如worker_processes只能存在于main中，而有的指令可以存在于多个层级，在这种情况下，子block会继承父block的配置，同时如果子block配置了与父block不同的指令，则会覆盖掉父block的配置。指令的格式是“指令名 参数1  参数2 … 参数N;”，注意参数间可用任意数量空格分隔，最后要加分号。

在开发Nginx HTTP扩展模块过程中，需要特别注意的是main、server和location三个层级，因为扩展模块通常允许指定新的配置指令在这三个层级中。

一般来说，一个server block表示一个Host，而里面的一个location则代表一个路由映射规则，这两个block可以说是HTTP配置的核心。配置文件

下图是Nginx配置文件通常结构图示。

![](http://bnetpi.qiniudn.com/nginx/1.png?imageView2/2/w/300)

### 2.2 主模块

> 控制 Nginx 的基本功能的指令.

#### 2.2.1 daemon

语法: daemon on | off

默认值: on

```
daemon  off;

```

生产环境中不要使用"daemon"和"master_process"指令，这些选项仅用于开发调试。

#### 2.2.2  debug_points

语法: debug_points [stop | abort]

默认值: none

```
debug_points stop;
```

#### 2.2.3 error_log

语法: error_log file [ debug | info | notice | warn | error | crit ]

默认值: ${prefix}/logs/error.log

Nginx 添加 --with-debug 编译参数, 你还能够使用以下配置:

```
error_log LOGFILE [ debug_core | debug_alloc | debug_mutex | debug_event
]: | debug_http | debug_imap ;

```

#### 2.2.4 include

语法: include file | *

默认值: none

你可以在任意地方使用include指令实现配置文件的包含，类似于apache中的include方法，可减少主配置文件d。

include 指令还支持像下面配置一样的全局包含的方法，例如包含一个目录下所有以".conf"结尾的文件:
```
include vhosts/*.conf;

```
注意路径受到configure编译参数--prefix=<路径>指令的影响，如果没有指定，Nginx默认是被编译在/usr/local/nginx。

语法: lock_file file

默认值: compile-time option
```
lock_file  /var/log/lock_file;
```

#### 2.2.5 master_process

语法: master_process on | off

默认值: on
```
master_process  off;

```
生产环境中不要使用"daemon"和"master_process"指令，这些选项仅用于开发调试。

#### 2.2.6 pid

语法: pid file

默认值: compile-time option Example:

```
pid /var/log/nginx.pid;
```
进程id存储文件。可以使用 kill -HUP cat /var/log/nginx.pid\ 对Nginx进行配置文件重新加载。

#### 2.2.7 ssl_engine

语法: ssl_engine engine

默认值: system dependent

该指令用于指定openssl使用的引擎。你可以通过下面的命令行获知系统目前支持的openssl引擎

openssl engine -t

例如 :
```
$ openssl engine -t
(cryptodev) BSD cryptodev engine
: [ available ]
(dynamic) Dynamic engine loading support
: [ unavailable ]
```

#### 2.2.8 timer_resolution

语法: timer_resolution t

默认值: none

Example:
```
timer_resolution  100ms;

```
官方文档的说明:

The directive allows to decrease number gettimeofday() syscalls. By default gettimeofday() is called after each return from kevent(), epoll, /dev/poll, select(), poll().

But if you need an exact time in logs when logging $upstream_response_time, or $msec variables, then you should use timer_resolution.

#### 2.2.9 user

语法: user user [group]

默认值: nobody nobody

指定Nginx Worker进程运行用户，默认是nobody帐号。

例如:

```
user www users;

```

#### 2.2.10 worker_cpu_affinity

语法: worker_cpu_affinity cpumask [cpumask...]

默认值: none

Linux only.

With this option you can bind the worker process to a CPU, it calls sched_setaffinity().

仅适用于linux，使用该选项可以绑定worker进程和CPU.

e.g.:
```
worker_proceses     4;
worker_cpu_affinity 0001 0010 0100 1000;
```

Bind each worker process to one CPU only.

分别给每个worker进程绑定一个CPU.

```
worker_proceses     2;
worker_cpu_affinity 0101 1010;

```

Bind the first worker to CPU0/CPU2, bind the second worker to CPU1/CPU3. This is suitable for HTT.

将CPU0/CPU2绑定给第一个worker进程，将CPU1/CPU3绑定给第二个worker进程。

#### 2.2.11 worker_priority

语法: worker_priority [-] number

默认值: on

With this option you can give to all worker processes the priority (nice) you need/wish, it calls setpriority().

使用该选项可以给所有的worker进程分配优先值。

#### 2.2.12 worker_processes

语法: worker_processes number

默认值: 1

e.g.:

```
worker_processes 5;
```
nginx has the ability to use more than one worker process for several reasons:

nginx可以使用多个worker进程，原因如下：

    1.to use SMP
    2.to decrease latency when workers blockend on disk I/O
    3.to limit number of connections per process when select()/poll() is used

The worker_processes and worker_connections from the event sections allows you to calculate maxclients value: k

max_clients = worker_processes * worker_connections

#### 2.2.13 worker_rlimit_core

语法: worker_rlimit_core size

默认值: '

Maximum size of core file per worker;

#### 2.2.14 worker_rlimit_nofile

语法: worker_rlimit_nofile limit 默认值: '

Specifies the value for maximum file descriptors that can be opened by this process.

指定

#### 2.2.15 worker_rlimit_sigpending

语法: worker_rlimit_sigpending limit 默认值: '

(Since Linux 2.6.8) Specifies the limit on the number of signals that may be queued for the real user ID of the calling process.

#### 2.2.16 working_directory

语法: working_directory path 默认值: --prefix

This is the working directory for the workers. It's used for core files only. nginx uses absolute paths only, all relative paths in configuration files are relative to --prefix==PATH.

### 2.3 事件模块

> 设置Nginx处理连接请求
e.g.:
```
events {
    worker_connections  1024;
}
```

#### 2.3.1 accept_mutex

Syntax: accept_mutex [ on | off ]

Default: on

nginx 使用连接互斥锁进行顺序的accept()系统调用.
#### 2.3.2 accept_mutex_delay

Syntax: accept_mutex_delay Nms;

Default: 500ms

如果一个进程没有互斥锁，它将延迟至少多长时间。默认情况下，延迟是500ms 。
#### 2.3.3 use
Syntax: use [ kqueue | rtsig | epoll | /dev/poll | select | poll | eventport ]

Default:


如果在./configure的时候指定了不止一种事件模型，那么可以设置其中一个，以便告诉nginx使用哪种事件模型。默认情况下nginx会在./configure时找出最适合系统的事件模型。

你可以在 这里 查看可用的事件模型以及如何在./configure时激活

#### 2.3.4 worker_connections

Syntax: worker_connections number

Default:

通过worker_connections和worker_proceses可以计算出maxclients：
```
max_clients = worker_processes * worker_connections
```
作为反向代理，max_clients为：
```
max_clients = worker_processes * worker_connections/4
```
Since a browser opens 2 connections by default to a server and nginx uses the fds (file descriptors) from the same pool to connect to the upstream backend

> 注 ：转载 请注明出处 [http://blog.netpi.me/nginx/nginx-conf/](http://blog.netpi.me/nginx/nginx-conf/)
