# Linux 常用命令
## lsof
`lsof` 是一个用于查看系统中打开文件的工具，它的名称是 "List Open Files" 的缩写。lsof 命令可以用于列出当前系统上所有进程打开的文件、网络连接和目录，以及这些文件的相关信息。它通常用于诊断问题、监视进程以及了解系统中文件和网络资源的使用情况。

```Shell
lsof [options]

默认：列出所有活跃进程的所有打开文件
-a: 结果与运算
-l : 在输出显示用户ID而不是用户名
-h : 获得帮助
-F : 格式化输出结果，用于其它命令。可以通过多种方式格式化，如-F pcfn（用于进程id、命令名、文件描述符、文件名，并以空终止）
......
```

### 获取网络信息
```Shell
lsof -i [options]

-i :port、TCP、6、@ip
```
* 显示所有网络连接：`lsof -i`;
* IPv6 流量：`lsof -i 6`
* 显示 TCP 连接：`lsof -i TCP`
* 特定端口：`lsof -i :22`
* 特定主机：`lsof -i@172.16.12.5`
* 组合主机端口：`lsof -i@172.16.12.5:22`
* 正在等待连接的端口：`lsof -i -sTCP:LISTEN` or `lsof -i | grep -i LISTEN`
* 已经建立连接的请求: `lsof -i -sTCP:ESTABLISHED`

### 获取用户信息
获取各种用户的信息，以及它们在系统上正干着的事情，包括它们的网络活动、对文件的操作等。
* 查看指定用户打开了什么：`lsof -u root`
* 杀死指定用户的所做的所有事情：kill -9 `lsof -t -u daniel`

### 命令和进程
* 指定的命令正在使用的文件和网络连接：`lsof -c <command>`
* 指定 PID: `lsof -p <PID>`
* 只获取 PID：`lsof -p -t <command>`

### 目录与文件
* 显示与指定目录/文件交互的所有一切: `lsof /var/log/messages/<filename>`

### 参考
Linux man 手册: https://www.netadmintools.com/html/lsof.man.html
