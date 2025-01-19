# 列出本地主机上的镜像
命令：
> docker images

-a 列出本地所有镜像（含历史）

-p 只显示镜像 ID

```
REPOSITORY : 表示镜像的仓库源
TAG : 镜像的标签
IMAGE ID : 镜像的 ID
CREATED : 镜像创建的时间
SIZE : 镜像的大小
```

> 同一个仓库源可以有多个 TAG 版本，代表这个仓库源的不同版本，我们使用 REPOSITORY:TAG 来定义不同的镜像。
如果你不指定一个镜像的版本标签，例如你只使用 ubuntu，docker 将默认使用 ubuntn:latest 镜像

# 搜索仓库中 xxx 镜像的名字
命令：
>docker search

示例：
>docker search hello-world

--limit: 只列出 N 个镜像，默认显示 25 个

# 下载仓库镜像到本地
命令：
>docker pull 镜像名字[:TAG]

示例：
>docker pull hello-world
没有 TAG 就是最新版，等价于 docker pull 镜像名字:latest

# 查看镜像/容器/ 数据卷所占的空间
命令：
> docker system df

# 删除某个镜像名字 ID
命令： 
>docker rmi

示例：
> docker rmi -f $(docker images -aq) 强制删除所有镜像