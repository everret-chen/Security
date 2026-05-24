# 第十七关

*在这里，User Name受到了check_input()检查，但password没有，所以可以把passwd作为注入点，闭合方式是单引号（'）*
```php
function check_input($value)
	{
	if(!empty($value))
		{
		// truncation (see comments)
		$value = substr($value,0,20);
		}

		// Stripslashes if magic quotes enabled
		if (get_magic_quotes_gpc())
			{
			$value = stripslashes($value);
			}

		// Quote if not a number
		if (!ctype_digit($value))
			{
			$value = "'" . mysql_real_escape_string($value) . "'";
			}
		
	else
		{
		$value = intval($value);
		}
	return $value;
	}
```

```
addslashes()
addslashes() 函数返回在预定义字符之前添加反斜杠的字符串。
预定义字符是：
单引号（'）
双引号（"）
反斜杠（\）
NULL
提示：该函数可用于为存储在数据库中的字符串以及数据库查询语句准备字符串。
注释：默认地，PHP 对所有的 GET、POST 和 COOKIE 数据自动运行 addslashes()。所以您
不应对已转义过的字符串使用 addslashes()，因为这样会导致双层转义。遇到这种情况时可
以使用函数 get_magic_quotes_gpc() 进行检测。
语法：addslashes(string)
参数
string            必需。规定要转义的字符串。
返回值：           返回已转义的字符串。
PHP 版本：         4+

|| stripslashes()
函数删除由 addslashes() 函数添加的反斜杠。

|| mysql_real_escape_string()
函数转义 SQL 语句中使用的字符串中的特殊字符。
下列字符受影响：
 \x00
 \n
 \r
 \
 '
 "
 \x1a
如果成功，则该函数返回被转义的字符串。如果失败，则返回 false。
语法：mysql_real_escape_string(string,connection)
参数                    描述
string              必需。规定要转义的字符串。
connection          可选。规定 MySQL 连接。如果未规定，则使用上一个连接。
说明：本函数将 string 中的特殊字符转义，并考虑到连接的当前字符集，因此可以安全用
于 mysql_query()。
```
