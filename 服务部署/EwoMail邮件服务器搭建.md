# EwoMail邮件服务器搭建

EwoMail是基于Linux的开源邮件服务器软件，集成了众多优秀稳定的组件，是一个快速部署、简单高效、多语言、安全稳定的邮件解决方案，帮助你提升运维效率，降低 IT 成本，兼容主流的邮件客户端，同时支持电脑和手机邮件客户端。
**需求centos7/8 64位系统**

安装文档：http://doc.ewomail.com/docs/ewomail/jianjie

前期准备：

1、CentOS 7.4 x64 | 2C2G | 40G 系统盘 

2、注册好的公网域名，（国内的可以不用网站备案）

## 1）Centos7 防火墙操作

```
查看防火墙状态：firewall-cmd --state

关闭防火墙：systemctl stop firewalld.service
```

![box-1.1](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.1.png)

## 2）设置主机名

```
hostnamectl set-hostname mail.申请的域名
```

![box-1.2](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.2.png)

## 3）host配置

```
vi /etc/hosts
添加如下内容：
邮件服务器ip mail.申请的域名  申请的域名  smtp.申请的域名 imap.申请的域名
```

![box-1.3](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.3.png)

## 4）安装EwoMail

在线获取安装：http://www.ewomail.com/list-11.html

输入自己域名获取下载安装命令

![box-1.4](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.4.png)

在vps上直接执行该命令，直接默认安装。

如图出现该错误直接使用给出命令安装即可（图中重新执行该事务命令那条命令即可安装）。

![box-1.5](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.5.png)

安装成功后如下图所示：

![box-1.6](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.6.png)



## 5）获取DKIM

DKIM是电子邮件验证标准，域名密钥识别邮件标准，主要是用来防止被判定为垃圾邮件。每个域名都需要添加一个dkim的key，EwoMail默认安装后已自动添加主域名dkim，只需要设置好dkim的dns即可，官方文档：http://doc.ewomail.com/docs/ewomail//21

Centos7/8 使用命令进行获取

```
amavisd -c /etc/amavisd/amavisd.conf showkey
```

![box-1.7](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.7.png)

使用官方工具进行格式化,并且写入域名解析中即可：http://www.ewomail.com/list-20.html

![box-1.8](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.8.png)

## 6）端口服务介绍

邮箱管理后台：http://IP:8010 （默认账号 admin，密码 ewomail123）

![box-1.13](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.13.png)

web 邮件系统：http://IP:8000

![box-1.14](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.14.png)

## 7）端口未开启服务解决

（这个情况在暂时还没碰到。。。）

如果访问不成功，切端口未开启，需要进入安装目录找到 start.sh 使用命令进行安装

```
sh ./start.sh 申请的域名
```



## 8）查看端口服务是否正常开启

利用netstat -antlp查看8000与8010端口的开启情况

![box-1.15](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.15.png)

## 9）DNS配置

这里可以直接在server的控制台去配置：

域名解析处添加如下记录：

![box-1.16](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.16.png)

A记录

`当其他SMTP服务器知道mail.yourdomain.com负责yourdomain.com的邮件发送和接收后，还必须要知道mail.yourdomain.com这台主机的IP才能建立连接并发送邮件。A记录就是将主机名解析成IP地址的一个记录。`

```
mail.申请的域名
```

![box-1.17](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.17.png)

```
www.choiceway.top
```

![box-1.18](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.18.png)

```
smtp.choiceway.top
```

![box-1.19](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.19.png)

```
pop.choiceway.top
```

![box-1.20](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.20.png)

MX记录

`MX记录的作用是告诉全世界的SMTP服务器，你的邮箱服务器mail.yourdomain.com负责yourdomain.com的邮件发送和接收。`

`邮箱服务器的常见DNS名字是mail.yourdomain.com。如果一个域名有多个邮箱服务器，那么可以设置多个MX记录，并设置优先级。优先级用数字表示，数字越小表示优先级越高。`

```
 10      mail.yourdomain.com
```

![box-1.21](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.21.png)

SPF记录

`假设邮件服务器收到了一封邮件，来自主机的 IP 是xx.xx.xx.xx，并且声称发件人为email@example.com。为了确认发件人不是伪造的，邮件服务器会去查询example.com的 SPF 记录。如果该域的 SPF 记录设置允许 IP 为xx.xx.xx.xx的主机发送邮件，则服务器就认为这封邮件是合法的；如果不允许，则通常会退信，或将其标记为垃圾/仿冒邮件。`

```
v=spf1 a mx ip4:xx.xx.xx.xx -all
```

![box-1.22](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.22.png)

DKIM记录

```
dkim._domainkey		| v=DKIM1; h=sha256; k=rsa; t=y; p=MIIBIjANBgkqhk8AMIIBCgKCAQEAsVasIoen4KWLsy+/y1zpVu0uz8hhpMlFox0cUSNneAccyKl9p6KvuN9Jv2D/SpTa8ldU7BV/XdFLLwqMmjMa6xOynkHy2Lk5LF8YFup8gno68/ZM01TDyOMS5eWosALzTpURKaoAfVYTp4//7AlcjpohKkJ7fEGq+0p1fCU+8Zi0FaWvNIJm/9iw2VQG3S0VxExaRj+MY7cbCpBpgAE1KsUeQuRcgb/8TJn83C3bFZwjsy0oh9qv/xxUYrbeJMDshYhQIDAQAB
```

![box-1.23](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.23.png)

DMARC记录

```
_DMARC		v=DMARC1; p=none; sp=none;
```

![box-1.24](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.24.png)



## 10）邮件管理系统登录及配置

添加域名以及邮箱账号：
![box-1.25](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.25.png)

![box-1.26](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.26.png)

![box-1.27](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.27.png)

## 11）Web邮件管理系统

使用刚才添加的邮箱登录邮件管理系统，就可以利用当前邮箱发送邮件了。

![box-1.28](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.28.png)

可以正常接收就可以了

![box-1.29](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.29.png)

## 12）部署过程中遇到的问题

12.1安装完成之后，邮件服务器运行中server卡顿？

```
解决办法：原本以为因为服务器未做端口访问限制被外部攻击导致的，后来安装了社区版雷池还是没解决，最后查了安装文档，由于部署时采用的服务器性能比较低，邮件服务器运行过程中自带的杀毒软件是默认开启的会占用大量内存，造成服务器极其卡顿，可以采用以下方法关闭杀毒软件，释放内存
```

```
vim /etc/amavisd/amavisd.conf 
```

```
:9999 #移至最后一行
```

![box-1.9](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.9.png)

![box-1.10](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.10.png)

添加以下内容：

```
#在文件尾部加上该行参数
@bypass_virus_checks_maps = (1);

#最后按下esc键，输入:wq保存
```

```
#修改文件（参考上面的例子操作命令修改）
vim /usr/lib/systemd/system/amavisd.service
在 Wants=clamd@amavisd.service 前面加上#符号
#保存文件
```

![box-1.11](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.11.png)

输入以下命令即可完成杀毒软件的关闭

```
systemctl daemon-reload
systemctl stop clamd@amavisd
systemctl disable clamd@amavisd
systemctl restart amavisd
```

![box-1.12](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.12.png)

12.2 发送邮件时一直被接收端邮箱判定为垃圾邮件进垃圾邮箱，甚至拒收？

```
解决办法：
实际上我这里是用邮件测试网站先测试了下邮件的评分，发给下面这个测试邮箱就可以了
网址：https://www.mail-tester.com/
```

![box-1.31](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.31.png)

```
碰到的问题就是没有添加反向地址解析导致进垃圾邮箱甚至拒收：
反向DNS查找或者反向DNS解析(rDNS)用于判定一个域名与给定IP是否互相关联。
有些公司例如AOL会拒绝没有进行反向解析的发件服务器发出的所有消息，所以你必须确保你进行了反向解析。
在一个IP地址上你只能关联一个域名。
```

没添加rDNS之前，这里nslookup自己的serverip是解析到的名称不是mail.yourdomain.com：

![box-1.32](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.32.png)

在云服务器控制台添加rdns就可以了，我是华为云的vps,注意选对地理位置，要不找不到实例🤣：

![box-1.33](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.33.png)

ok,这样就行了，再试试nslookup:

![box-1.34](https://github.com/rootwlen/tinkerbox/tree/main/img/box-1.34.png)
