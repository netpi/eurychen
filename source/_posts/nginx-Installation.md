title: 安装 Nginx 服务器
date: 2014-10-20 10:10:19
tags:
- nginx
- code
categories:
- nginx
---
<blockquote class="blockquote-center">
Nginx 安装
</blockquote>

<!--more-->
## 1 ,安装前准备

安装nginx之前我们通过 yum 安装两个软件

1 pcre - 为了有rewrite功能，

```
# yum install pcre

```
2 openssl - 为了支持ssl

```
# yum install openssl

```

## 2 ,安装Nginx

### 2.1 下载 Nginx

我们用wget下载1.6.2 稳定版

```
wget http://nginx.org/download/nginx-1.6.2.tar.gz


```
### 2.2 解压并且安装
下载完成之后 解压文件

```
tar xvf nginx-1.6.2.tar.gz
```
解压完成之后 , 开始编译安装
```

# cd nginx-1.6.2


# ./configure --prefix=/usr/local/nginx-1.6.2 \
--with-http_ssl_module --with-http_spdy_module \
--with-http_stub_status_module --with-pcre

```
```
make
```
```
make install
```


#### 2.2.1 安装过程中常见错误1
```
./configure: error: the HTTP rewrite module requires the PCRE library.

```
那么我们要安装pcre-devel来解决问题

```
# yum -y install pcre-deve
```

#### 2.2.2 安装过程中常见错误 2

```
/configure: error: the HTTP cache module requires md5 functions
from OpenSSL library.   You can either disable the module by using
--without-http-cache option, or install the OpenSSL library into the system,
or build the OpenSSL library statically from the source with nginx by using
--with-http_ssl_module --with-openssl=<path> options.
```
解决办法

```
yum -y install openssl openssl-devel
```
## 2.3 启动Nginx

```
/usr/local/nginx-1.6.2/sbin/nginx

curl http://localhost
```
发现Nginx 服务已经正常启动 : )

## License


> 注 ：转载 请注明出处 [http://blog.netpi.me/nginx/nginx-Installation/](http://blog.netpi.me/nginx/nginx-Installation/)
