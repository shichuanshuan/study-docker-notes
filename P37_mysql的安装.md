# 安装
docker pull mysql:5.7

# 进入 mysql
命令：docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7


# 开启挂在点
作用：在 mysql 中添加库后，再删除 mysql 后，之前添加的库就没有了。
当开启挂在点后，重新开启 mysql，之前的数据还存在。

命令：
docker run -d -p 3306:3306 --privileged=true
-v /zzyyuse/mysql/log:/var/log/mysql
-v /zzyyuse/mysql/data:/var/lib/mysql
-v /zzyyuse/mysql/conf:/etc/mysql/conf.d
-e MYSQL_ROOT_PASSWORD=123456
--name mysql
mysql:5.7

其中 conf 目录是修改字符集
mysql 默认字符集不是 utf-8，所以中文数据无法写到库中。
