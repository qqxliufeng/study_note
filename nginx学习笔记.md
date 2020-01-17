## Nginx 学习笔记

### 个人理解：

1、在计算机上一个端口只能由一个程序（进程）监听，但是一个程序（进程）可以监听多个端口。

在nginx.conf中可以配置多个server，每个server可以配置不同的端口，这就相当于nginx这个程序（进程）监听了多个端口。

当一个请求进入到nginx的时候，默认用浏览器请求的时候，浏览器会自动给加上80端口，比如：访问`http://www.baidu.cok` 相当于访问了`http://www.baidu.com:80`。如果有显示的写上请求端口，浏览器则不会再加上这个默认的80端口，而是直接使用自定义的端口。

如果在nginx中配置了两个server都是监听的80，但是`server_name`一个是`www.a.com`一个是`www.b.com`，在浏览器请求`http://www.a.com`或者`http://www.b.com`的时候，nginx会根据配置的`server_name`自己内部查找对应的站点，从而实现了一个nginx可以配置多个80端口的网站。

如以下配置（`nginx.conf`）：

```.shell
server {
    listen       80;
    server_name  www.a.com;
    location / {
    	root   /Users/lf/Desktop/test_site/www.a.com;
    	index  index.html index.htm;
   }
}
server {
    listen       80;
    server_name  www.b.com;
    location / {
    	root   /Users/lf/Desktop/test_site/www.b.com;
    	index  index.html index.htm;
    }
}

```

