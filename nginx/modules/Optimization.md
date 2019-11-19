# nginx优化
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

## nginx优化
### worker_processes
worker_processes用来设置Nginx服务的进程数。推荐是CPU内核数或者内核数的倍数，推荐使用CPU内核数，因为我的CPU为4核的，所以设置为4。

### worker_cpu_affinity
默认情况下，Nginx的多个进程有可能跑在某一个CPU或CPU的某一核上，导致Nginx进程使用硬件的资源不均，因此绑定Nginx进程到不同的CPU上是为了充分利用硬件的多CPU多核资源的目的。
worker_cpu_affinity用来为每个进程分配CPU的工作内核，参数有多个二进制值表示，每一组代表一个进程，每组中的每一位代表该进程使用CPU的情况，1代表使用，0代表不使用。所以我们使用worker_cpu_affinity 0001 0010 0100 1000;来让进程分别绑定不同的核上。

### worker_connections
设置一个进程理论允许的最大连接数，理论上越大越好，但不可以超过worker_rlimit_nofile的值。还有个问题，linux系统中有个指令open file resource limit，它设置了进程可以打开的文件句柄数量，可以用下面的指令查看你的linux系统中open file resource limit指令的值，cat /proc/sys/fs/file-max
可以将该指令设置为23900251
echo "2390251" > /proc/sys/fs/file-max; sysctl -p

### worker_rlimit_nofile
设置毎个进程的最大文件打开数。如果不设的话上限就是系统的ulimit –n的数字，一般为65535。


### use epoll
设置事件驱动模型使用epoll。事件驱动模型有select、poll、poll等。
-   select先创建事件的描述符集合，对于一个描述符，可以关注其上面的Read事件、Write事件以及Exception事件，所以要创建三类事件描述符集合，分别用来处理Read事件的描述符、Write事件的描述符、Exception事件的描述符，然后调用底层的select()函数，等待事件发生，轮询所有事件描述符集合的每一个事件描述符，检查是否有事件发生，有的话就处理。select效率低，主要是轮询效率低，而且还要分别轮询三个事件描述符的集合。
-   poll方法与select类似，都是先创建一个关注事件的描述符集合，再去等待这些事件发生，然后再轮询描述符集合，检查有无事件发生，如果有，就去处理。不同点是poll为Read事件、Write事件以及Exception事件只创建一个集合，在每个描述符对应的结构上分别设置Read事件、Write事件以及Exception事件。最后轮询的时候，可以同时检察权这三个事件是否发生。可以说，poll库是select库的优化实现。
-   epoll是Nginx支持的高性能事件驱动库之一。是公认的非常优秀的事件驱动模型。和poll库跟select库有很大的不同，最大区别在于效率。我们知道poll库跟select库都是创建一个待处理的事件列表，然后把这个列表发给内核，返回的时候，再去轮询检查这个列表，以判断事件是否发生。这样在描述符多的应用中，效率就显得比较低下了。一种比较好的方式是把列表的管理交由内核负责，一旦某种事件发生，内核就把发生事件的描述符列表通知给进程，这样就避免了轮询整个描述符列表。首先，epoll库通过相关调用同志内核创建一个有N个描述符的事件列表，然后给这些描述符设置所关注的事件，并把它添加到内核的事件列表中去。完成设置以后，epoll库就开始等待内核通知事件发生了，某一事件发生后，内核讲发生事件的描述符列表上报给epoll库，得到事件列表的epoll库，就可以进行事件处理了。epoll库在linux平台是高效的，它支持一个进程打开大数目的事件描述符，上限是系统可以打开文件的最大数目；同时，epoll库的IO效率不随描述符数量的增加而线性下降，因为它只会对内核上报的活跃的描述符进行操作。

### accept_mutex
大致意思是当某一时刻只有一个网络连接到来时，多个睡眠进程会被同时叫醒，但只有一个进程可获得连接，如果每次唤醒的进程数目太多，会影响一部分系统性能。在Nginx服务器的多进程下，就可能出现这个问题，为了解决这个问题，Nginx配置了包含这样一条指令accept_mutex，当其设置为开启的时候，将会对多个Nginx进程接受连接进行序列化，防止多个进程对连接的争抢。当服务器连接数不多时，开启这个参数会让负载有一定程度的降低。但是当服务器的吞吐量很大时，为了效率，请关闭这个参数；并且关闭这个参数的时候也可以让请求在多个worker间的分配更均衡。所以我们设置accept_mutex off;

### sendfile
使用开启或关闭是否使用sendfile()传输文件，普通应用应该设为on，下载等IO重负荷的应用应该设为off，因为大文件不适合放到buffer中。
传统文件传输中（read／write方式）在实现上3其实是比较复杂的，需要经过多次上下文切换，当需要对一个文件传输时，传统方式是：
-   调用read函数，文件数据被copy到内核缓冲区
-   read函数返回，数据从内核缓冲区copy到用户缓冲区
-   write函数调用，将文件数据从用户缓冲区copy到内核与socket相关的缓冲区
-   数据从socket缓冲区copy到相关协议引擎
从上面可以看出来，传统readwrite进行网络文件传输的方式，在过程中经历了四次copy操作。

硬盘－>内核buffer->用户buffer->socket相关缓冲区->协议引擎
而sendfile系统调用则提供了一种减少多次copy，提高文件传输性能的方法。流程如下：
-   sendfile系统效用，文件数据被copy至内核缓冲区
-   记录数据文职和长度相关的数据保存到socket相关缓存区
-   实际数据由DMA模块直接发送到协议引擎

### tcp_nopush
sendfile为on时这里也应该设为on，数据包会累积一下再一起传输，可以提高一些传输效率。

### tcp_nodelay
小的数据包不等待直接传输。默认为on。
看上去是和tcp_nopush相反的功能，但是两边都为on时nginx也可以平衡这两个功能的使用。

### keepalive_timeout
HTTP连接的持续时间。设的太长会使无用的线程变的太多。这个根据自己服务器访问数量、处理速度以及网络状况方面考虑。

### send_timeout
设置Nginx服务器响应客户端的超时时间，这个超时时间只针对两个客户端和服务器建立连接后，某次活动之间的时间，如果这个时间后，客户端没有任何活动，Nginx服务器将关闭连接，将其设置为10s，Nginx与客户端建立连接后，某次会话中服务器等待客户端响应超过10s，就会自动关闭。

### types_hash_max_size
types_hash_max_size影响散列表的冲突率。types_hash_max_size越大，就会消耗更多的内存，但散列key的冲突率会降低，检索速度就更快。types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升。

### client_header_buffer_size
该指令用于设置Nginx服务器允许的客户端请求头部的缓冲区大小，默认为1KB，此指令的赋值可以根据系统分页大小来设置，分页大小可以用以下命令获取getconf PAGESIZE。

### client_max_body_size 8m
客户端上传的body的最大值。超过最大值就会发生413(Request Entity Too Large)错误。默认为1m，最好根据自己的情况改大一点。

## gzip
### gzip on
启用gzip，对响应数据进行在线实时压缩,减少数据传输量。

### gzip_disable
Nginx服务器在响应这些种类的客户端请求时，不使用Gzip功能缓存应用数据，gzip_disable "msie6"对IE6浏览器的数据不进行GZIP压缩。

### gzip_min_length
Gzip压缩功能对大数据的压缩效果明显，但是如果压缩很小的数据，可能出现越压缩数据量越大的情况，因此应该根据相应页面的大小，选择性开启或者关闭Gzip功能。建议将值设置为1KB。

### gzip_comp_level
设置压缩程度，包括级别1到级别9，级别1表示压缩程度最低，压缩效率最高；级别9表示压缩程度最高，压缩效率最低，最费时间。

### gzip_vary
用于设置在使用Gzip功能时是否发送带有“Vary：Accept-Encoding”头域的响应头部，该头域的主要功能是告诉接收方发送的数据经过了压缩处理，开启后端效果是在响应头部Accept-Encoding: gzip，对于本身不支持Gzip的压缩的客户端浏览器是有用的。

### gzip_buffers;
该指令用于设置Gzip压缩文件使用存储空间的大小，
语法gzip_buffers number size;number，指定Nginx服务器需要向系统申请存储空间的个数，size，指定每个缓存空间的大小。根据配置项，Nginx服务器在对响应输出数据进行Gzip压缩时需向系统申请numbersize大小的空间用于存储压缩数据。默认情况下，numbersize的值为128，其中size的值为系统内存页一页的大小，用getconf PAGESIZE来获取

### gunzip_static
开启时，如果客户端浏览器不支持Gzip处理，Nginx服务器将返回解压后的数据，如果客户端浏览器支持Gzip处理，Nginx服务器忽略该指令设置。仍然返回压缩数据。

### gzip_types
Nginx服务器可以根据MIME类型选择性开启Gzip压缩功能。该指令涌来设置MIME类型。
