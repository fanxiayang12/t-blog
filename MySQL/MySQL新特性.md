# MySQL 5.7
## generated column
```sql
CREATE TABLE triangle (
  sidea DOUBLE,
  sideb DOUBLE,
  sidec DOUBLE AS (SQRT(sidea * sidea + sideb * sideb))
);
INSERT INTO triangle (sidea, sideb) VALUES(1,1),(3,4),(6,8);
```
[参考链接](http://mingxinglai.com/cn/2015/12/mysql5.7-virtal-column/)