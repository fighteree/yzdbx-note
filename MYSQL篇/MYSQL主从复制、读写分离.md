>[!note] 作用
>**如果数据库使用读的场景比较多时，为了避免写操作所带来的性能影响 可以采用读写分离的结构。往主库写，从库读，主库同步给从库。解决了写操作对查询性能的影响。**

![[Pasted image 20241127132044.png]]




## 从主复制原理

**实现整个主从复制，需要由slave服务器上的IO进程和Sql进程共同完成要实现主从复制，首先必须打开Master端的binary log（bin-log）功能，因为MySQL主从复制过程实际上就是Slave从Master端获取相应的二进制日志，然后再在自己slave端完全按照顺序执行日志中所记录的各种操作。**


其主从复制流程图与步骤如下：

![[Pasted image 20241127132744.png]]

- 步骤一：主库db的更新事件(update、insert、delete)被写到binlog
- 步骤二：从库发起连接，连接到主库
- 步骤三：此时主库创建一个binlog dump thread线程，把binlog的内容发送到从库
- 步骤四：从库启动之后，创建一个I/O线程，读取主库传过来的binlog内容并写入到relay log.
- 步骤五：从库还会创建一个SQL线程，从relay log里面读取内容，将更新内容写入到slave的db.


## 环境搭建

```
192.168.18.130主库---192.168.18.131从库
```


1. **首先需要关闭防火墙**
```shell
 systemctl stop firewalld
 systemctl disable firewalld
```

2. **设置serve_id （主库）**
```shell
[mysqld]
...
# 开启二进制日志（必须）
log-bin = mysql-bin
# MySQL服务ID，保证整个集群环境中唯一，默认为1（必须）
server-id = 1
# 二进制日志格式，默认ROW（可选）
binlog_format = ROW
# 忽略的数据，不需要同步的数据库
# binlog-ignore-db = mysql
# 指定同步的数据库
# binlog-do-db = db01
```
 **从库**
 ```shell
 [mysqld]
...
# 开启二进制日志（必须）
log-bin = mysql-bin
# MySQL服务ID，保证整个集群环境中唯一，默认为1（必须）
server-id = 2
# 二进制日志格式，默认ROW（可选）
binlog_format = ROW
# 是否只读，1代表只读，0代表读写
read-only = 1
```

3. **重启`MySQL`服务器。**
```shell
systemctl restart mysql
```

4. **登录`MySQL`数据库，创建远程连接的账号，并授予主从复制权限。**
```shell
# 创建yzdbx用户，并设置密码，该用户可在任意主机连接该MySQL服务
CREATE USER 'yzdbx'@'%' IDENTIFIED BY 'Root_12root';
# 为'yzdbx'@'%'用户分配主从复制权限
grant replication slave on *.* to 'yzdbx'@'%';
```

5. **通过指令，查看二进制日志坐标**
```shell
#注意mysql8.4.3版本非稳定版不支持主从复制
show master status;
```
![[Pasted image 20241127145035.png]]

6. **登录`MySQL`数据库，在从库设置主库配置。**
```shell
#MySQL8.0.23之前的版本，执行如下`SQL`语句
change master to master_host='xxx.xxx.xxx.xxx',master_user='xxx',master_password='xxx',master_log_file='xxx',master_log_pos=xxx;

#MySQL8.0.23之后的版本，执行如下`SQL`语句
change replication source to source_host='xxx.xxx.xxx.xxx',source_user='xxx',source_password='xxx',source_log_file='xxx',source_log_pos=xxx;
```
**参数含义：**

| 参数名             | 含义             | 8.0.23之前        |
| --------------- | -------------- | --------------- |
| source_host     | 主库IP地址         | master_host     |
| source_user     | 连接主库的用户名       | master_user     |
| source_password | 连接主库的密码        | master_password |
| source_log_file | binlog日志文件名    | master_log_file |
| source_log_pos  | binlong日志文件位\| | master_log_pos  |


7. **开启同步操作(在从库操作)**
```shell
# 8.0.22之后
start replica; 
# 8.0.22之前
start slave;```

8. **查看主从同步状态**
```shell
# 8.0.22之后
show replica status; 
# 8.0.22之前
show slave status;
```
![[Pasted image 20241127143238.png]]

>[!warning] 注意事项
>修改`/var/lib/mysql/auto.cnf`文件。将`server-uuid`属性修改为唯一值。


