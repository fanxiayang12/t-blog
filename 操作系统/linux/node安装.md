[参考链接](https://www.jianshu.com/p/c35a580b197d)

wget https://nodejs.org/dist/v10.13.0/node-v10.13.0-linux-x64.tar.xz
tar -xvf node-v10.13.0-linux-x64.tar.xz

sudo su
mv node-v10.13.0-linux-x64 /usr/local/node

PATH=/usr/local/node/bin:$PATH
echo $PATH