官方解析：
http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html
中国人总结的：
http://blog.csdn.net/joewolf/article/details/4876604


pom.xml 配置下载远程仓库

<project>  
...  
  <repositories>  
    <repository>  
      <id>maven-net-cn</id>  
      <name>Maven China Mirror</name>  
      <url>http://maven.net.cn/content/groups/public/</url>  
      <releases>  
        <enabled>true</enabled>  
      </releases>  
      <snapshots>  
        <enabled>false</enabled>  
      </snapshots>  
    </repository>  
  </repositories>  
  <pluginRepositories>  
    <pluginRepository>  
      <id>maven-net-cn</id>  
      <name>Maven China Mirror</name>  
      <url>http://maven.net.cn/content/groups/public/</url>  
      <releases>  
        <enabled>true</enabled>  
      </releases>  
      <snapshots>  
        <enabled>false</enabled>  
      </snapshots>      
    </pluginRepository>  
  </pluginRepositories>  
...  
</project>  

settings.xml 配置下载远程仓库

<settings>  
  ...   
  <profiles>  
    <profile>  
      <id>dev</id>  
      <!-- repositories and pluginRepositories here-->  
    </profile>  
  </profiles>  
  <activeProfiles>  
    <activeProfile>dev</activeProfile>  
  </activeProfiles>  
  ...   
</settings>

或通过mirror去配置
<mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
    <mirror>
      <id>nexus-public-repository-group</id>
      <mirrorOf>*</mirrorOf>
      <name>nexus Public Repository Group</name>
      <url>http://192.168.9.100:9087/nexus/content/groups/public/</url>
    </mirror>
  </mirrors>


pom.mxl 配置部署远程仓库

<project>    
  ...    
  <distributionManagement>    
    <repository>    
      <id>nexus-releases</id>    
      <name>Nexus Release Repository</name>    
      <url>http://127.0.0.1:8080/nexus/content/repositories/releases/</url>    
    </repository>    
    <snapshotRepository>    
      <id>nexus-snapshots</id>    
      <name>Nexus Snapshot Repository</name>    
      <url>http://127.0.0.1:8080/nexus/content/repositories/snapshots/</url>    
    </snapshotRepository>    
  </distributionManagement>    
  ...    
</project>    

根据项目版本，判断将构件部署到那个仓库
release、snapshot

部署构件，需要认证信息，没有认证信息，报401错误。在settings.xml中配置
<settings>     
  ...     
  <servers>     
    <server>     
      <id>nexus-releases</id>     
      <username>admin</username>     
      <password>admin123</password>     
    </server>     
    <server>     
      <id>nexus-snapshots</id>     
      <username>admin</username>     
      <password>admin123</password>     
    </server>       
  </servers>     
  ...     
</settings>
