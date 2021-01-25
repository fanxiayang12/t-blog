# mvn私服
## CentOS
### 路径

[Download](https://help.sonatype.com/repomanager3/download/download-archives---repository-manager-3)

`cd /home/nexus`
### 启动 nexus
```sh
export RUN_AS_USER=root # 如果启动失败，先执行命令
/home/nexus/bin/nexus start
```
## Docker
[Image](https://hub.docker.com/r/sonatype/nexus3/)
### start
```sh
    # binding the exposed port 8081 to the host
    docker run -d -p 8081:8081 --name nexus sonatype/nexus3
```

### stop
```sh
    # When stopping, be sure to allow sufficient time for the databases to fully shut down.
    docker stop --time=120 nexus
```
### log
`docker logs -f nexus`

## 管理端
    http://127.0.0.1:8081

    admin
    cat /nexus-data/admin.password
## Maven 仓库配置

pom.xml

```xml
<repositories>
    <repository>
        <id>nexus</id>
        <url>http://maven.1mdata.com/repository/maven-public/</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
</repositories>
```

settings.xml

```xml
<server>
    <id>nexus</id>
    <username>username</username>
    <password>password</password>
</server>
```
## 参考资料
- [开源中国](https://www.oschina.net/p/nexus?hmsr=aladdin1e1)
- [新版搭建](https://blog.csdn.net/chenfei2341/article/details/80431700)
- [老版搭建](http://www.ccblog.cn/1.htm)