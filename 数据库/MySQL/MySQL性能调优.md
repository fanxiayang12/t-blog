# 插入数据

1. 加大mysql配置中的bulk_insert_buffer_size
    这个参数默认为8M
    bulk_insert_buffer_size=100M
2. 改写所有insert语句为insert delayed
    这个insert delayed不同之处在于：立即返回结果，后台进行处理插入。

```sql
set session bulk_insert_buffer_size=10000388608
show variables like '%bulk_insert_buffer_size%';
```
[参考链接](http://www.45it.com/database/201412/38310.htm)

----