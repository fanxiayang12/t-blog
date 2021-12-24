[参考文档](https://www.jianshu.com/p/e010bb69deba)
```shell
wget https://downloads.mongodb.org/linux/mongodb-linux-x86_64-amazon2-4.4.4.tgz
tar -zxvf mongodb-linux-x86_64-amazon2-4.4.4.tgz
sudo mv ~/mongodb-linux-x86_64-amazon2-4.4.4 /usr/local/mongodb

# client
scp /Users/alegria/MyWorkSpace/emdata-prize/emdata-prize-egg/.bash/mongod.service prize:~/
# server
sudo su
mv mongod.service /usr/lib/systemd/system/
mkdir /var/lib/mongo
mkdir /var/log/mongodb
systemctl daemon-reload
systemctl start mongod.service
systemctl enable mongod.service
systemctl is-enabled mongod.service
```