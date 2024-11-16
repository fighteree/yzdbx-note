
**学习目录：**

- [[#MYSQL慢查询]]





## MYSQL慢查询

>[!note] 说明
>**mysql慢查询指sql语句执行耗时较长超过预期，一下的几种情况会导致慢sql的情况发生：**
>- sql中有大量聚合查询
>- sql连表过多
>- 单表数据量过大
>- 深度分页

### 如何定位慢查询？

1. **利用三方开源工具：**

    - 调试工具: `Arthas`
    - 运维工具: [Skywalking](https://skywalking.apache.org/)


2. **mysql自带慢日志文件:**

	在mysql的配置文件/etc/my.cnf中添加如下配置表示开启慢日志：
	![[Pasted image 20241116103415.png]]

```sql
-- 执行查询语句检查慢查询日志是否已经开启
SHOW VARIABLES LIKE 'slow_query_log';
-- 通过以下命令查看慢查询日志文件的路径
SHOW VARIABLES LIKE 'slow_query_log_file';`
```
