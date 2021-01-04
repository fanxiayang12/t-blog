Spring Boot 自带的编译插件 spring-boot-maven-plugin

# 默认用该插件编译打包的jar为 启动可执行包，不可以被依赖
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

# 取消生成启动可执行包，只生成依赖包
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>
```

# 同时生成可执行包和可依赖包
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <!-- 生成可执行的jar的名字：xxx-exec.jar -->
        <!-- 不固定，写成abcd都可以 -->
        <classifier>exec</classifier>
    </configuration>
</plugin>
```
