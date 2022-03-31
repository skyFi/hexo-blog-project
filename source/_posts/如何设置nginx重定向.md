---
title: 如何设置nginx重定向
date: 2018-09-21 23:51:39
tags: 笔记
---

![](/images/roadblock.jpg)

nginx 是一个灵活且高效的网络服务器，如果想要在nginx服务器中重定，你可以从下面挑选一个适合的方式。

## 简单且快速的 return

这是一个非常简单的设置方式，只需要个`return`语句就可以了
```ruby
return 301 https://example.com$request_uri;
```
你需要把这段代码放到nginx配置文件的`server`代码块中，`301`是永久重定向，你也可以设置成`302`做一个临时重定向（不建议）。

一个完整的例子：
```ruby
server {
    listen 80;
    listen [::]:80;
    hostname example.com www.example.com;
    return 301 https://example.com$request_uri;
}
```

## 正则表达式 rewrite

如果`return`不能满足你的复杂业务需求，你可以考虑下正则匹配重定向：
```javascript
rewrite ^/foo/(bar)/(.*)$ https://$server_name/$1/$2 permanent;
```
同样这也是需要在`server`代码块中，其中`permanent`为`301`永久跳转，若需要`302`可修改为`redirect`

一个完整的例子：
```ruby
server {
    listen 80;
    listen [::]:80;
    hostname example.com www.example.com;
    root /var/www/example.com/public;
    rewrite ^/foo/(bar)/(.*)$ $scheme://$server_name/$1/$2 permanent;
}
```
又如：
```ruby
server {
    listen       80;
    server_name   www.fangyongle.com  fangyongle.cn;
    if ($host != 'www.fangyongle.com' ) { 
        rewrite ^/(.*)$ https://www.fangyongle.com/$1 permanent; 
    } 
}
```
再如：
```ruby
# 根据文件类型设置过期时间
location ~* \.(js|css|jpg|jpeg|gif|png|swf)$ {
    if (-f $request_filename) {
       expires    1h;
       break;
    }
}
```

## 使用Maps

如果你有一堆需要重定向的连接映射，你可以考虑在一个地方定义它，然后再通过`if`来手动判断重定向。

首先定义重定向链接映射`redirect-map.conf`
```ruby
map $request_uri $redirect_uri {
    /about.html          /about-us;
    /customers.html      /our-customers;
    /products.html       /our-products;
}
```
然后在`server`代码块使用：
```ruby
include redirect-map.conf;
server {
    […]
    if ( $redirect_uri ) {
        return 301 $redirect_uri;
    }
}
```

映射也可以有一些语法：
```ruby
map $request_uri $redirect_uri {
    /about.html          /about-us;
    /customers.html      /our-customers;
    /products.html       /our-products;
    # Match any url that ends in products.html or producs.htm
    ~products\.html?$    /our-products;
    # case-insensitive version of the above
    ~*products\.html?$   /our-products;
    # A named capture that maps
    # e.g. product-1234.html into /products/item-1234/overview
    ~product-(?<sku>\d+)\.html   /products/item-$sku/overview;
}
```

## 一些实用的重定向例子

### http 重定向为 https
```ruby
return 301 https://$host$request_uri;
```

### 统一规范域名
```ruby
server_name example.com www.example.com example.net www.example.net _;
if ( $host != $server_name ) {
    return 301 $scheme://$server_name$request_uri;
}
```

### 含 www 和 不含 www 之间的重定向
```ruby
# non-www to www
if ( $host !~ ^www\. ) {
    return 301 $scheme://www.$host$request_uri;
}
```
```ruby
# www to non-www
if ( $host ~ ^www\.(?<domain>.+)$ ) {
    return 301 $scheme://$domain$request_uri;
}
```

## 附录

### 重定向中常用全局变量

```javascript
$scheme		       // HTTP方法（如http，https）,如：http
$host			   // 请求主机头字段，否则为服务器名称，如:www.fangyongle.com
$server_name	   // 服务器名称，如：www.fangyongle.com
$request_uri	   // 包含请求参数的原始URI，不包含主机名，如：/2018/81.html?a=1&b=2
$request_filename  // 当前请求的文件的路径名，由root或alias和URI request组合而成，如：/2013/81.html
```

### nginx 部分常用全局变量
```javascript
$remote_addr		//获取客户端ip
$binary_remote_addr	//客户端ip（二进制)
$remote_port		//客户端port，如：50472
$remote_user		//已经经过Auth Basic Module验证的用户名
$host			//请求主机头字段，否则为服务器名称，如:www.fangyongle.com
$request		//用户请求信息，如：GET ?a=1&b=2 HTTP/1.1
$request_filename	//当前请求的文件的路径名，由root或alias和URI request组合而成，如：/2013/81.html
$status			//请求的响应状态码,如:200
$body_bytes_sent        // 响应时送出的body字节数数量。即使连接中断，这个数据也是精确的,如：40
$content_length	       // 等于请求行的“Content_Length”的值
$content_type	       // 等于请求行的“Content_Type”的值
$http_referer	       // 引用地址
$http_user_agent      // 客户端agent信息,如：Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.76 Safari/537.36
$args		     //与$query_string相同 等于当中URL的参数(GET)，如a=1&b=2
$document_uri	     //与$uri相同  这个变量指当前的请求URI，不包括任何参数(见$args) 如:/2018/81.html
$document_root	     //针对当前请求的根路径设置值
$hostname	     //如：centos53.localdomain
$http_cookie	    //客户端cookie信息
$cookie_COOKIE	    //cookie COOKIE变量的值
$is_args	//如果有$args参数，这个变量等于”?”，否则等于”"，空值，如?
$limit_rate	//这个变量可以限制连接速率，0表示不限速
$query_string	    // 与$args相同 等于当中URL的参数(GET)，如a=1&b=2
$request_body	   // 记录POST过来的数据信息
$request_body_file	//客户端请求主体信息的临时文件名
$request_method	      //客户端请求的动作，通常为GET或POST,如：GET
$request_uri	      //包含请求参数的原始URI，不包含主机名，如：/2018/81.html?a=1&b=2
$scheme		       //HTTP方法（如http，https）,如：http
$uri			//这个变量指当前的请求URI，不包括任何参数(见$args) 如:/2018/81.html
$request_completion	//如果请求结束，设置为OK. 当请求未结束或如果该请求不是请求链串的最后一个时，为空(Empty)，如：OK
$server_protocol	//请求使用的协议，通常是HTTP/1.0或HTTP/1.1，如：HTTP/1.1
$server_addr		//服务器IP地址，在完成一次系统调用后可以确定这个值
$server_name		//服务器名称，如：www.fangyongle.com
$server_port		//请求到达服务器的端口号,如：80
```

### Rewrite正则相关指令详解

nginx的`rewrite`相当于apache的`rewriterule`(大多数情况下可以把原有apache的`rewrite`规则加上引号就可以直接使用)，它可以用在`server`,`location`和`IF`条件判断块中，命令格式如下：
```javascript
rewrite <regex> <replacement> <flag>
```
#### 正则表达式匹配

* `~`为区分大小写匹配
* `~*`为不区分大小写匹配
* `!~`和`!~*`分别为区分大小写不匹配及不区分大小写不匹配

#### 文件及目录匹配判断

* `-f`和`!-f`用来判断是否存在文件
* `-d`和`!-d`用来判断是否存在目录
* `-e`和`!-e`用来判断是否存在文件或目录
* `-x`和`!-x`用来判断文件是否可执行

#### flag标记

* `last` - 基本上都用这个Flag。
* `break` - 中止`rewirte`，不在继续匹配
* `redirect` - 返回临时重定向的HTTP状态302
* `permanent` - 返回永久重定向的HTTP状态301

使用`last`和`break`实现`URI`重写，浏览器地址栏不变。而且两者有细微差别：

* 使用`alias`指令必须用`last`标记
* 使用`proxy_pass`指令时，需要使用`break`标记
* `last`标记在本条`rewrite`规则执行完毕后，会对其所在`server{......}`标签重新发起请求，而`break`标记则在本条规则匹配完成后，终止匹配。 
