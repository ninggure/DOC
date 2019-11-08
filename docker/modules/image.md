# docker镜像使用
## 列出镜像列表
```
    docker images
```

## 获取一个新的镜像
```
    docker pull xxxx
    xxxx: 镜像名|镜像名:版本号
```

## 查找镜像
```
    docker search xxxx
    xxxx: 镜像名
```

## 删除镜像
```
    docker rmi hello-world
```

## 创建镜像
-   更新镜像
```
    docker commit -m="x" -a="xx" xxxx xxxxx
    x: 提交的描述信息
    xx: 指定镜像作者
    xxxx: 容器ID
    xxxxx: 指定要创建的目标镜像名
```

## 设置镜像标签
```
    docker tag xxxx xxxxx
    xxxx: 镜像ID
    xxxxx: 镜像名: 标签(一般指版本号)
```

