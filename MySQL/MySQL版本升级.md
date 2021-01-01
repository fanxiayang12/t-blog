# MySQL 5.6 升级到MySQL 5.7 (Linux)

## 升级资料

1. 查看mysql安装包
https://www.electrictoolbox.com/yum-list-installed-packages/
2. 安装教程
http://linuxresolved.com/upgrade-mysql-5-6-mysql-5-7-centos/
3. mysql.sock
http://blog.51cto.com/pengjc/1861088
4. mysql_upgrade -u root

## 升级异常和问题
### Error occurred: Cannot setup server variables
https://dba.stackexchange.com/questions/129525/mysql-mysql-upgrade-error-occurred-cannot-setup-server-variables


## 操作步骤

1. Download MySQL 5.7 PRM
    `wget http://repo.mysql.com/mysql57-community-release-el7.rpm -P /tmp/`
2. 查看并移除MySQL RPM contains MySQL 5.6
```b
yum list installed | grep mysql
yum remove mysql-community-release
// rpm -e --nodeps
```
3. Install MySQL 5.7 RPM
```
rpm -ivh /tmp/mysql57-community-release-el7.rpm
```
4. Update MySQL Repository to 5.7
`yum update mysql`
5. upgrade check
```mysql_upgrade -u root```
### mysql.sock
```
find / -name mysql.sock
vim /etc/my.cnf
[mysqld]
	socket=/tmp/mysql.sock
ln -s /tmp/mysql.sock /var/lib/mysql/mysql.sock
ln -s /data/emdata/mysql/mysql.sock /var/lib/mysql/mysql.sock
```
### Cannot setup server variables
restart mysql with 
```
vim /usr/lib/systemd/system/mysqld.service
mysqld --skip-grant-tables
```

## 服务操作
```
systemctl status mysqld.service
systemctl daemon-reload
systemctl restart mysqld.service
```

## 升级最佳实战
http://blog.51cto.com/lisea/1941616

https://yq.aliyun.com/articles/543336

https://dev.mysql.com/doc/refman/5.7/en/upgrading.html