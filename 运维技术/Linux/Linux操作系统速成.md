# Linux速成
## 什么是Linux？
## 用户管理
三种用户
 - 超级管理员（uid=0）
 - 系统用户(uid=1~999)
 - 普通用户(uid>=1000)
### 用户和组文件
- /etc/passwd
- /etc/shadow
- /etc/group
- /etc/gshadow
### 管理命令

## 文件权限管理

### umask值
- 修改umask方法
### ACL权限细化
- setfacl
- getfacl

## 网络和系统管理
### 网络
- ifconfig
- ping
- netstat
### 系统
- ps
- top
- kill和killall

## Linux安全加固
### 1. 身份鉴别
1.1 是否存在空密码账户
1.2 检查密码有效期
1.3 检查密码修改最小间隔时间
1.4 确保密码到期警告为7天或以上
1.5 确保root是唯一的uid=0的账户
1.6 检查密码复杂度要求
1.7 检查密码重用是否受限制
### 2. 网络配置
2.1 完善防火墙配置，仅开放必要端口
2.2 对ssh进行加固
2.3 修改默认端口
2.4 禁用密码认证，使用密钥登录
2.5 限制IP访问
