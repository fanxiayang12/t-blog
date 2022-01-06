# 场景
    jenkins等CI/CD集成系统，将构建后的文件自动上传Seafile服务器
# 标签
    Linux服务器、Seafile
# 参考文献
    [命令行手册](https://blog.csdn.net/potatomagic/article/details/53172178)
    [Seafile Client for Cli server](https://help.seafile.com/syncing_client/linux-cli/)
# 样例
```sh
mkdir .seaf
seaf-cli init -d ~/.seaf

seaf-cli start

seaf-cli list
seaf-cli list-remote -s https://cloud.1mdata.com -u jenkins@1mdata.com -p jenkins
seaf-cli status
seaf-cli download -l 29e00cea-0a74-4e51-9576-653d5e4d7132 -s https://cloud.1mdata.com -d ~/libraries -u jenkins@1mdata.com -p jenkins
seaf-cli desync -d ~/libraries/本地版数据库-elinks/
seaf-cli sync -l 29e00cea-0a74-4e51-9576-653d5e4d7132 -s https://cloud.1mdata.com -d ~/libraries/本地版数据库-elinks/ -u jenkins@1mdata.com -p jenkins

seaf-cli stop

tail -f ~/.ccnet/logs/seafile.log
```