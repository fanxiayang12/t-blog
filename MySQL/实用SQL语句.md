# 数据操作
## 指定编码创建数据库
`CREATE DATABASE IF NOT EXISTS database DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`
## 清空指定库里满足某些特征表的数据
`select
	CONCAT("truncate table ",table_name,";")
		from information_schema.tables 
		    where TABLE_SCHEMA = 'emdata_emr'
			    and TABLE_NAME like 'ip_%'`

## 查询mysql实例中所有库名
`select CONCAT(TABLE_SCHEMA) from information_schema.tables 	
	where true
		and TABLE_SCHEMA like 'emdata_emr_%'	
		and TABLE_NAME = 'op_medical_history_info'`
# 数据备份
1. 备份
`mysqldump -u root -p databaase > database.sql`

2. copy 从A服务器scp到另外一台服务器B
scp -r -i ./1m_hdp_rsa ./database.sql root@10.10.10.82:~

3. 恢复
   + 建库  
`mysql -u root -p
CREATE DATABASE IF NOT EXISTS database DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`
   + 恢复 
`mysql -u root -p database < database.sql`
    [参考文章](https://blog.csdn.net/win_turn/article/details/60880990)

## Backup a local database and restore to remote server using single command:

`mysqldump -u root -ptmppassword sugarcrm | mysql \
                 -u root -ptmppassword --host=remote-server -C sugarcrm1`

`mysqldump -u root demo | mysql -u root -h 127.0.0.1 -C demo_copy`

# 管理操作
## 查看版本
`select VERSION()`    
## 实时查看mysql连接进程列表
`show full processlist` 
## 查看mysql最大连接数
`show variables like '%max_connections%';`
