# 1. 新建主服务器容器实例 3307
命令：
docker run -p 3307:3306 --name mysql-master \
-v /Users/shics/opt/docker-datas/mysql-master/log:/var/log/mysql \
-v /Users/shics/opt/docker-datas/mysql-master/data:/var/lib/mysql \
-v /Users/shics/opt/docker-datas/mysql-master/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql

# 2. 进入 /mydata/mysql-master/conf 目录下添加 my.cof 配置文件
内容如下：
```
[mysqld]
## 设置server_id，同一局域网中需要唯一
server_id = 101
## 执行不需要同步的数据库名称
binlog-ignore-db=mysql
## 开启二进制日志功能
log-bin=mall-mysql-bin
## 设置二进制日志使用内存大小（事物）
binlog_cache_size=1M
## 设置使用的二进制日志格式（mixed，statement，row）
binlog_format=mixed
## 二进制日志过期清理时间。默认值为0，表示不自动清理
expire_logs_days=7
## 跳过主从配置中遇到的所有错误或指定类型的错误，避免 slave端复置终端。
slave_skip_errors=1062
```

# 3. 修改完配置后重启 master 实例
docker restart mysql-master


# 4. 进入 mysql-master 容器
docker exec -it mysql-master /bin/bash

`进入数据库后：`
mysql -uroot -p
root

`查看数据库：`
show databases;

# 5. master 容器实例内创建数据同步用户
CREATE USER 'slave'@'%' IDENTIFIED BY '123456';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';

# 6. 新建从服务器容器实例 3308
命令：
docker run -p 3308:3306 --name mysql-slave \
-v /Users/shics/opt/docker-datas/mysql-slave/log:/var/log/mysql \
-v /Users/shics/opt/docker-datas/mysql-slave/data:/var/lib/mysql \
-v /Users/shics/opt/docker-datas/mysql-slave/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql

# 7. 进入 /mydata/mysql-slave/conf 目录下新建my.cnf
内容如下：
```
[mysqld]
## 设置server_id，同一局域网中需要唯一
server_id = 102
## 执行不需要同步的数据库名称
binlog-ignore-db=mysql
## 开启二进制日志功能,以备Slave作为其他数据实例的Master时使用
log-bin=mall-mysql-slave1-bin
## 设置二进制日志使用内存大小（事物）
binlog_cache_size=1M
## 设置使用的二进制日志格式（mixed，statement，row）
binlog_format=mixed
## 二进制日志过期清理时间。默认值为0，表示不自动清理
expire_logs_days=7
## 跳过主从配置中遇到的所有错误或指定类型的错误，避免 slave端复置终端。
slave_skip_errors=1062
## relay_log配置中继日志
relay_log=mall-mysql-relay-bin
## log_slave_updates 表示 slave 将复置事件写进自己的二进制日志
log_slave_updates=1
## slave 设置为只读（具有super权限的用户除外）
read_only=1
```

# 8. 修改完配置后重启 slave 实例
docker restart mysql-slave

# 9. 在主数据库中查看主从同步状态
show master status;

# 10. 进入 mysql-slave 容器
docker exec -it mysql-slave /bin/bash

`进入数据库后：`
mysql -uroot -p
root



# 11. 在从数据库中配置主从复制
`绑定主数据库`
命令：

>change master to master_host='宿主机IP',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=617,master_connect_retry=30;

master_log_file是show master status;命令输出的File字段，master_log_pos是Position字段

示例：

>change master to master_host='192.168.111.63',master_user='slave',master_password='123456',master_port=3307,master_log_file='mall-mysql-bin.000001',master_log_pos=617,master_connect_retry=30;

# 12. 在从数据库中查看主从同步状态
show slave status \G;

**看什么**
```
Slave_IO_Running: No
Slave_SQL_Running: No

No:表示没开始
```


# 13. 在从数据库中开启主从同步
mysql>start slave;

# 14. 查看从数据库状态发现已经同步
show slave status \G;

查看 No 是否变 Yes

# 15. 主从复制测试
在主数据库中创建数据，查看从数据中是否有同步数据