## 实测
```shell
# json
rm -rf mongo.db/thrid
mongodump -h 10.10.10.145 --port 27017 -d thrid -o mongo.db
scp -r mongo.db prize:~

# https://www.mongodb.com/try/download/database-tools
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-amazon2-x86_64-100.5.0.tgz
tar -zxvf mongodb-database-tools-amazon2-x86_64-100.5.0.tgz

/usr/local/mongodb/bin/mongorestore --drop ~/mongo.db