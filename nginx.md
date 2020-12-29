# 默认路径
## 执行程序
    /usr/local/nginx/sbin/nginx
## 配置文件
    /usr/local/nginx/conf/nginx.conf
# find nginx
## by process
`ps -ef | grep nginx`
## by name
`find / -name nginx`
# control nginx
## 验证config
`nginx -t`
## 启动
`nginx -c nginx.conf`
## 重启
`nginx -s reload`
## 停止
`kill -9 2072`

