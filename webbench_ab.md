### ab

#### 功能

```shell
Apache的Web服务器的性能测试工具，它可以测试安装Web服务器每秒种处理的HTTP请求，也可以用于测试nginx性能
```

#### 语法

```shell
ab [选项] [参数] [http[s]://]hostname[:port]/path
# 常用的参数有 -c -n -t
-A：指定连接服务器的基本的认证凭据；
-c：指定一次向服务器发出请求数；
-C：添加cookie；
-g：将测试结果输出为“gnuolot”文件；
-h：显示帮助信息；
-H：为请求追加一个额外的头；
-i：使用“head”请求方式；
-k：激活HTTP中的“keepAlive”特性；
-n：指定测试会话使用的请求数；
-p：指定包含数据的文件；
-q：不显示进度百分比；
-t：测试的时间
-T：使用POST数据时，设置内容类型头；
-v：设置详细模式等级；
-w：以HTML表格方式打印结果；
-x：以表格方式输出时，设置表格的属性；
-X：使用指定的代理服务器发送请求；
-y：以表格方式输出时，设置表格属性。
```

#### 实例

```shell
ab -c 10 -n 100 http:49.234.146.164:6699/
```

#### 错误处理

```shell
# apr_socket_recv: Connection reset by peer (104)
[root@aa~]# This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
 
Benchmarking 192.168.1.176 (be patient)
Completed 300 requests
Completed 600 requests
Completed 900 requests
apr_socket_recv: Connection reset by peer (104)
Total of 1085 requests completed
 
查看应用服务器和数据库均未报错，连接被重置，bingyi了以下，apr_socket_recv这个是操作系统内核的一个参数，在高并发的情况下，内核会认为系统受到了SYN flood攻击，会发送cookies（possible SYN flooding on port 80. Sending cookies），这样会减慢影响请求的速度，所以在应用服务武器上设置下这个参数为0禁用系统保护就可以进行大并发测试了：
# vim /etc/sysctl.conf 
net.ipv4.tcp_syncookies = 0
# sysctl -p
然后就可以超过1000个并发测试了。
 
另附其他系统内核参数说明：
 
net.ipv4.tcp_syncookies = 0  
#此参数是为了防止洪水攻击的，但对于大并发系统，要禁用此设置
 
net.ipv4.tcp_max_syn_backlog
#参数决定了SYN_RECV状态队列的数量，一般默认值为512或者1024，即超过这个数量，系统将不再接受新的TCP连接请求，一定程度上可以防止系统资源耗尽。可根据情况增加该值以接受更多的连接请求。
 
net.ipv4.tcp_tw_recycle
#参数决定是否加速TIME_WAIT的sockets的回收，默认为0。
 
net.ipv4.tcp_tw_reuse
#参数决定是否可将TIME_WAIT状态的sockets用于新的TCP连接，默认为0。
 
net.ipv4.tcp_max_tw_buckets
#参数决定TIME_WAIT状态的sockets总数量，可根据连接数和系统资源需要进行设置。
```

#### 参考

- [ab命令压力测试](https://www.cnblogs.com/yueminghai/p/6412254.html)
- [压力测试报错](https://www.cnblogs.com/archoncap/p/5883723.html)

