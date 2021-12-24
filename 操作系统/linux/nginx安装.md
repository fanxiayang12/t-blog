## windows

    [Windows Server 2016](https://blog.csdn.net/h610443955/article/details/81096506)
    [Windows Service Wrapper](https://blog.csdn.net/bitree1/article/details/100559530)
    [反向代理外网](https://blog.csdn.net/chenyulancn/article/details/70841298?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link)


## linux

[参考链接](https://www.runoob.com/linux/nginx-install-setup.html)

wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar zxvf nginx-1.12.2.tar.gz

sudo su
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
cd nginx-1.12.2/
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
make
make install
/usr/local/nginx/sbin/nginx -t

systemctl start nginx.service
systemctl restart nginx.service
systemctl enable nginx.service
systemctl is-enabled nginx.service

/usr/local/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/nginx/sbin/nginx -s stop              # 停止 Nginx

scp /Users/alegria/MyWorkSpace/emdata-prize/emdata-prize-egg/.bash/www.spaclens.com.conf prize:~/

sudo su

cd /usr/local/nginx/conf
vim nginx.conf
    include conf.d/*.conf;
mkdir conf.d

mv www.spaclens.com.conf /usr/local/nginx/conf/conf.d/www.spaclens.com.conf