# Trigger：
    
```sql
create trigger trg_adminInfo_insert on adminInfo for insert 
    as 
    update adminIinfo_Sum set adminInfoSum = adminInfoSum + 1 where adminInfoId = 1 
```