## 高版本jdk找不到低版本的jdk tools解决办法
```xml
<dependency>
    <groupId>jdk.tools</groupId>
    <artifactId>jdk.tools</artifactId>
    <version>1.7</version>
    <scope>system</scope>
    <systemPath>/Library/Java/JavaVirtualMachines/jdk1.8.0_102.jdk/Contents/Home/lib/tools.jar</systemPath>
</dependency>
```