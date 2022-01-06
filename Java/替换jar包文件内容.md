# 场景
    目前前后端分离开发趋势下，前端打包后的dist目录，需要在后端构建之前放入resource目录下，再打包后端，生成最终jar；在jenkins自动构建流程中，改用分别打包，打包完后，将前端dist替换入后端jar包的resource下，其间也会对其他修改操作。
# [参考文献](https://www.pianshen.com/article/6961356938/)
# 命令行
```sh
# 查看
jar tvf emdata-dc-boot-${VERSION}.jar BOOT-INF/classes
# 解压
jar xvf emdata-dc-boot-${VERSION}.jar BOOT-INF/classes
# 压缩
jar uvf emdata-dc-boot-${VERSION}.jar BOOT-INF/classes
```