---
title: 本地配置DNS服务器（MAC版）
date: 2018-07-26 16:44:59
tags: DNS
---

作为一个前端开发者，会遇到使用`cookie`的情况，常见的如：登录，权限控制，视频播放，图形验证码等，这时候本地开发者在PC上会使用修改`hosts`的方式添加指向本地的域名，来获取`cookie`的同域名。如：
```
127.0.0.1 local.skylor.com
```
但是在移动端的时候，这一招就不好使了，苹果手机在没越狱的情况下是没法修改`hosts`的，难道为了这个让自己的爱机越个狱？答案应该是否定的。那么怎么处理这个问题呢？完美解决方案就是配置本地局域网的__DNS服务__。于是我就查询了一系列的文档文章，总结出这篇文章，踩过些许坑，希望后来人别在重复踩了，由于本人使用的是Mac，所以下面的一些命令亲和Mac党，其他系统也类似，只有写系统命令可能不同，废话不多说，君且详看：

## 安装
```
brew update; brew install dnsmasq;
```
## 启动
```
sudo brew services start dnsmasq
```
## 重启
```
sudo brew services restart dnsmasq
```
## 停止
```
sudo brew services stop dnsmasq
```
## 查看dnsmasq的运行
```
ps aux | grep dnsmasq
```
### 找到运行参数
````
/usr/local/opt/dnsmasq/sbin/dnsmasq --keep-in-foreground -C /usr/local/etc/dnsmasq.conf
```
### 找到运行配置文件：
```
/usr/local/etc/dnsmasq.conf
```
### 编辑他，这里用vim：
```
sudo vim /usr/local/etc/dnsmasq.conf
```
干下面这些事情，去掉以下注释，并修改，具体配置是干啥的，后面会有讲到：
```
no-resolv
no-poll
listen-address=127.0.0.1,172.17.7.115
addn-hosts=/etc/dnsmasq.hosts
```
## 添加自定义hosts文件dnsmasq.hosts
```
sudo vim /etc/dnsmasq.hosts
```
里面添加你需要填写的域名对应关系，如：
```
172.17.7.115 yongle.skylor.com
```
重启下你的DNS服务器，启动代码在文章上面有说到，截至目前，你的Mac本地的DNS配置就OK了，但是让同局域网的其他设备使用的话，还需要一些操作。
## 修改路由器的DHCP
配置DNS
```
172.17.7.115
```
备用DNS配置为一般常用DNS服务器就好了。

局域网内设备需重连WIFI，而后就可以使用你本地的DNS服务了，检测是否成功，可以使用`dig`命令，如你本机上可这么来：
```
dig yongle.skylor.com @127.0.0.1
```
当你看到
```
yongle.skylor.com.	0	IN	A	172.17.7.115
```
说明成功了，这时候应该就结束了，但若还有局域网内的设备连不上的话，可以查看下该设备的DNS是否含有`172.17.7.115`，若没有，重启wifi连接，就OK了。
