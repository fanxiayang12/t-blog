# [Arthas](https://arthas.aliyun.com/doc/index.html)
## 文件目录

~/.arthas/

~/logs/arthas   

## 下载

    curl -O https://arthas.aliyun.com/arthas-boot.jar
    
    java -jar arthas-boot.jar --repo-mirror aliyun --use-http

## 命令
```sh
# help
# java -jar arthas-boot.jar -h

# auto select
# java -jar arthas-boot.jar

# attach by pid
# ps -ef | grep java
# java -jar arthas-boot.jar pid

# java -jar arthas-boot.jar 24388
java -jar arthas-boot.jar 24388 --target-ip 10.10.10.15

# quit temporarily
# quit
# exit

# stop completely
# stop
```

## 集成到spring boot

    应用启动后，spring会启动arthas，并attach自身进程。
    结合Endpoint的actuator，可以查看arthas配置

    http://localhost:8080/actuator/arthas
## Web Console

    --target-ip参数指定listen的IP，attach成功之后，可以直接访问:http://10.10.10.15:3658/