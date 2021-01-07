
# 利用ELK收集分布式日志

# ELK

[官方网站](https://www.elastic.co/cn/elastic-stack)

+ Elasticsearch
+ Logstash
    + stop
  
```shell
ps -ef | grep logstash
kill -9 id
```

+ start


```shell
nohup ./bin/logstash -f ./config/logstash.conf > ./logs/logstash.log 2>&1 </dev/null &
tail -f logs/logstash.log
```

+ logstash-sample.conf

    根据不同的语音，区分存储到不同的索引中。

```conf
input {
  tcp {
    port => 3456
    codec => json
    type => "java"
  }
  tcp {
    port => 5678
    codec => json
    type => "node"
  }
}

output {
  if [type] == "node" {
    elasticsearch {
      hosts => ["http://10.10.10.40:9200"]
      index => "node_logs_%{+YYYY.MM.dd}"
    }
  }

  if [type] == "java" {
    elasticsearch {
      hosts => ["http://10.10.10.40:9200"]
      index => "java_logs_%{+YYYY.MM.dd}"
    }
  }

  # stdout {
      # codec => rubydebug
  # }
}
```

[Logstash](https://www.elastic.co/guide/en/logstash/current/index.html)

[logstash.conf](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html)


+ Kibana

    http://10.10.10.40:5601/

    Management -> Index Patterns -> Create Index Pattern

    Discover -> <java_logs_*> -> Add a filter -> Save Search

## Spring Boot
### 依赖
```xml
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>6.6</version>
</dependency>
```
### logback-spring.xml
#### tcp版

    根据不同环境，自定义配置日志输出策略

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/base.xml" />

    <springProperty scope="context" name="app_name" source="spring.application.name" />

    <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <destination>10.10.10.16:3456</destination>
        <encoder charset="UTF-8" class="net.logstash.logback.encoder.LogstashEncoder"></encoder>
    </appender>

    <springProfile name="dev">
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
        </root>
    </springProfile>
    <springProfile name="remote">
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
        </root>
    </springProfile>
    <springProfile name="prod">
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="LOGSTASH" />
        </root>
    </springProfile>
</configuration>
```
#### udp版
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/base.xml" />
    <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashUdpSocketAppender">
        <host>10.10.10.16</host>
        <!-- port is optional (default value shown) -->
        <port>5678</port>
        <!-- layout is required -->
        <layout class="net.logstash.logback.layout.LogstashLayout" />
    </appender>
    <root level="all">
        <appender-ref ref="LOGSTASH" />
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

# 心得

    相比较原先的日志查看方式，由于做了负载均衡，不确定日志会在那台服务器输出，需要同时登录不同服务器tail日志文件。现在，在Kibana中，可以一眼查看到所有实例的日志输出，并且可以自定义快捷检索，大大缩短了错误日志定位的时间，提升了效率。因此，工作中遇到比较繁杂任务时，不妨更深入的思考下，是不会自己选择的工作方式有继续改进的空间，往往能将自己的思维提升到更广阔的空间。

# 参考文章
<https://www.cnblogs.com/woshimrf/p/elk-springboot.html>

