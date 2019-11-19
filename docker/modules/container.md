# docker容器使用
## 查看容器
```
    docker ps
    默认打印结果为已启动的容器
    -a: 显示所有容器(包括已停止的容器)
    -no-trunc: 取消信息省略
```

## 启动容器
启动已有镜像的容器，如果没有对应的镜像，会先从仓库中下载下来后创建容器
```
    docker run ubuntu:lastest /bin/bash
    参数说明:
        -i: 交互式操作
        -t: 终端,一般和i一起用,-it
        ubuntu:lastest: ubuntu镜像名+版本，版本可以忽略，默认为最新版
        /bin/bash: 命令
        -d: 后台运行，即使退出终端容器任然运行着，并返回容器ID
        -v 本地目录/文件路径:挂载在docker上的路径: 绑定一个卷，挂载文件
        -p 主机端口:容器端口:指定端口映射 
        -P: 随机端口映射
        --name="xxxx" :容器名
        --net="xxxx": 指定容器网络连接类型,类型:bridge|host|none|container
```
## 启动已停止运行的容器
```
    docker start xxxx
    xxxx: 容器ID
```

## 停止容器
```
    docker stop xxxx
    xxxx: 容器ID
```

## 删除容器
``` 
    docker rm -f xxxx
    xxxx: 容器ID
```

## 重启容器
```
    docker restart xxxx
    xxxx: 容器ID
```

## 进入容器
该方法可以在容器中执行命令
```
    docker exec -it xxxx xxxxxx
    xxxx: 容器ID
    xxxxx: 指令
    如: docker exec -it 1e505154646 ifconfig:查看容器ip地址
```

执行多条指令
```
    docker exec -it xxx /bin/bash "cd ... && vi ..."
    xxx: 容器ID或容器名
    "..": 命令，多条命令用&&隔开
```
## 导出和导入容器
-   导出容器
```
    docker export xxxx > xxxxx
    xxxx: 容器ID
    xxxxx: 保存的目录
```
-   导入容器快照
```
    docker import xxxx xxxxx:xxx
    xxxx: 快照路径
    xxxxx: 容器名
    xxx: 版本号

    docker import xxxx
    xxxx: URL
```

## 容器互联
-   查看网络信息
```
    docker network ls
```
-   新建网络
```
    docker network create -d xxx xxxx
    xxx: 指定容器网络连接类型,类型:bridge|host|none|container
    xxxx: 网络名
```
-   连接容器
```
    docker run -itd --name xxx --network=xxxx xxxxx xxxxxx
    xxx:    容器名
    xxxx:   网络名
    xxxxx:  镜像名
    xxxxxx: 命令
```
-   查看容器ip
```
    docker inspect xxx | grep IPAddress
    xxx: 容器名
```

## 重新配置容器
```
    1.把容器commit成镜像
    docker commit xxx  xxxx
    xxx: 容器名
    xxxx: 镜像名
    
    2.可以删除原来的容器，也可以后面再删除
    
    3.使用docker run ...重新配置和创建容器
```
