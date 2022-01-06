# mac以及linux上对多版本node管理利器n使用汇总

```bash
# 查看版本
n
# 切换以及安装版本
sudo n <version>
# 删除版本
sudo n rm <version>
```

## 采坑
### 安装异常后，node -v提示Segment fault
[参考链接](https://blog.csdn.net/weixin_42020084/article/details/105212687)

```bash
# 移除所有版本之后，重新安装新版本
cd /usr/local/n/
rm -rf versions
```