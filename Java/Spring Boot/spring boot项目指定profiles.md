http://www.jspxcms.com/knowledge/341.html

Maven启动指定Profile
mvn spring-boot:run -P prod
Spring Boot指定spring.profiles.active
spring-boot 1.x
mvn spring-boot:run -Drun.profiles=prod
spring-boot 2.x
mvn spring-boot:run -Dspring-boot.run.profiles=prod
Jar文件运行
java -jar -Dspring.profiles.active=prod