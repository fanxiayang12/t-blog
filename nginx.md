# 默认路径
## 执行程序
    /usr/local/nginx/sbin/nginx
## 配置文件
    /usr/local/nginx/conf/nginx.conf

### server_name 匹配规则
[官方文档](https://nginx.org/en/docs/http/server_names.html)

[匹配规则](https://blog.csdn.net/daiyudong2020/article/details/73863437)

[参考文档](https://www.cnblogs.com/fengff/p/12597770.html)

[default_server](https://segmentfault.com/a/1190000015681272)

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

