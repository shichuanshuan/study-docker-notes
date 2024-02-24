# docker run
命令：docker run 镜像名

作用：新建或启动容器

--name=“容器新名字” 为容器指定一个名词；不填随机生成名字

-d：后台运行容器并返回容器 ID，也即启动守护式容器

-i：以交互模式运行容器，通常与 -t 同时使用

-t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；

-P：随机端口映射

-p：指定端口映射

# docker ps
命令：
>docker ps [OPTIONS]

作用：
>列出当前所有正在运行的容器

-a: 列出当前所有正在运行的容器 + 历史上运行过的

-l：显示最近创建的容器

-n：显示最近 n 个创建的容器

-q：静默模式，只显示容器编号

# 退出容器
方式1
> exit：run 进去容器，exit退出，容器停止

方式2
> ctrl+p+q：run 进去容器，ctrl+p+q退出，容器不停止

# docker start 
命令：
> docker start 容器 ID 或容器名

作用：
> 启动已停止运行的容器

# docker restart
命令：
> docker restart 容器 ID 或容器名

作用：
> 重启容器

# docker stop
命令：
> docker stop 容器 ID 或容器名

作用：
>停止容器

# docker kill
命令：
> docker kill 容器 ID 或容器名

作用：
> 强制停止容器

# docker rm 
命令：
> docker rm 容器 ID (rmi 是删除镜像)

作用：
> 删除已停止的容器

一次性删除多个容器实例 docker rm -f $(docker ps -a -q)

# 重要
## 启动守护式容器（后台服务器）
命令：
> docker run -d 容器名

redis 前台启动演示
> 前台交互式启动

```docker run -it redis```

> 后台交互式启动

```docker run -d redis```

## 查看容器日志
命令：
> docker logs 容器ID

## 查看容器内运行的进程
命令：
> docker top 容器ID

## 查看容器内部细节
命令：
> docker inspect 容器ID

## 进入正在运行的容器并以命令行交互
命令：

> docker exec -it 容器ID bashShell
> 
> 或
> 
> docker attach 容器ID
上述两个区别
```
attach 直接进入容器启动命令的终端，不会启动新的进程，用 exit 退出，会导致容器的停止

exec 是在容器中打开新的终端，并且可以启动新的进程，用 exit 退出，不会导致容器的停止。
```
推荐使用 docker exec 命令，因为退出容器终端，不会导致容器的停止。

# 从容器中拷贝文件到主机上
命令：
> docker cp 容器:ID 容器内路径 目的主机路径

# 导入和导出容器
命令：
> docker export 容器ID > 文件名.tar

作用：
> 导出容器的内容留作为一个 tar 归档文件

命令：
> cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号

例如：
> cat abc.tar | docker import -t shics/ubuntu:3.7

作用：
> import 从 tar 包中的内容创建一个新的文件系统再导入为镜像