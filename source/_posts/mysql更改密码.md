---
title: mysql更改密码
date: 2018-04-06 15:33:36
tags:
---
# 虽然简单,但也有坑

 1. 使用set password方式

```
set password for root@localhost = password(‘123’); 
```
 2. mysql5.6.6版本之后增加了密码强调验证插件validate_password,validate_password_length 默认为8
```
mysql> alter user root@'localhost' identified by 'oracle';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
---报错，密码不满足制定的密码负责度要求
```
 3. 解决方法如下
  ```
 mysql> set global validate_password_policy=0;  
Query OK, 0 rows affected (0.05 sec)  
  
mysql>   
mysql>   
mysql> set global validate_password_mixed_case_count=0;  
Query OK, 0 rows affected (0.00 sec)  
  
mysql> set global validate_password_number_count=3;  
Query OK, 0 rows affected (0.00 sec)  
  
mysql> set global validate_password_special_char_count=0;  
Query OK, 0 rows affected (0.00 sec)  
  
mysql> set global validate_password_length=3;  
Query OK, 0 rows affected (0.00 sec)  
```
    


