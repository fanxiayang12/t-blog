[如何查看cpu核数](https://blog.csdn.net/fox_wayen/article/details/80642718)

```sh
# CPU型号
cat /proc/cpuinfo | grep name | sort | uniq
# CPU个数
cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l

# 需安装
lscpu
```