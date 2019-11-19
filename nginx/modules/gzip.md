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
