# NFS远程目录挂载
    https://www.cnblogs.com/merely/p/10793877.html
# 客户端
```sh
yum -y install nfs-utils
mkdir  /data/nfs
mount 10.10.10.162:/data/nfs /data/nfs
```