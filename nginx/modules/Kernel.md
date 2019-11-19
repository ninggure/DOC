
## 内核参数优化
把如下的参数追加到Linux系统的/etc/sysctl.conf文件中，然后使用如下命令使修改生效：/sbin/sysctl -p
```
    net.core.netdev_max_backlog = 262144
    net.core.somaxconn = 262144
    net.ipv4.tcp_max_orphans = 262144
    net.ipv4.tcp_max_syn_backlog = 262144
    net.ipv4.tcp_timestamps = 0
    net.ipv4.tcp_synack_retries = 1
    net.ipv4.tcp_syn_retries = 1
```
### net.core.netdev_max_backlog参数
参数net.core.netdev_max_backlog,表示当每个网络接口接受数据包的速率比内核处理这些包的速率快时，允许发送队列的数据包的最大数目，我们调整为262144.


### net.core.somaxconn
该参数用于调节系统同时发起的TCP连接数，一般默认值为128，在客户端高并发的请求的情况下，该默认值较小，可能导致连接超时或者重传问题，我们可以根据实际情况结合并发数来调节此值。

### net.ipv4.tcp_max_orphans
该参数用于设定系统中最多允许存在多少TCP套接字不被关联到任何一个用户文件句柄上，如果超过这个数字，没有与用户文件句柄关联到TCP套接字将立即被复位，同时发出警告信息，这个限制只是为了简单防治Dos攻击，一般系统内存充足的情况下，可以增大这个参数。

### net.ipv4.tcp_max_syn_backlog
该参数用于记录尚未收到客户端确认信息的连接请求的最大值，对于拥有128内存的系统而言，此参数的默认值为1024，对小内存的系统则是128，一般在系统内存比较充足的情况下，可以增大这个参数的赋值。

### net.ipv4.tcp_timestamps
该参数用于设置时间戳，这个可以避免序列号的卷绕，在一个1Gb/s的链路上，遇到以前用过的序列号概率很大，当此值赋值为0时，警用对于TCP时间戳的支持，默认情况下，TCP协议会让内核接受这种异常的数据包，针对Nginx服务器来说，建议将其关闭。

### net.ipv4.tcp_synack_retries
该参数用于设置内核放弃TCP连接之前向客户端发送SYN+ACK包的数量，为了建立对端的连接服务，服务器和客户端需要进行三次握手，第二次握手期间，内核需要发送SYN并附带一个回应前一个SYN的ACK，这个参数主要影响这个过程，一般赋予值为1，即内核放弃连接之前发送一次SYN＋ACK包。

