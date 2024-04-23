# Apache HTTP Server 

Apache HTTP Server (aka Apache Web Server or httpd). HTTP daemon

Apache HTTP Server 项目使用 Subversion 托管其源代码。 GitHub 的只读镜像

httpd.conf 里面的配置  configuration directive 配置指令/命令 例如DocumentRoot directory-path	、<VirtualHost addr[:port] [addr[:port]] ...> ... </VirtualHost>

其中<...> 可叫 section directive 或 directive 或  section container

https://httpd.apache.org/docs/2.4/mod/directives.html  
https://httpd.apache.org/docs/2.4/en/mod/quickreference.html  
https://httpd.apache.org/docs/2.4/sections.html

该DocumentRoot指令指定应将这些文件放置在文件系统中的位置。该指令可以全局设置，也可以按虚拟主机设置。

错误日志的位置由ErrorLog指令定义，可以全局设置，也可以按虚拟主机设置。

该Listen指令不实现虚拟主机 - 它只告诉主服务器要侦听哪些地址和端口。如果不 <VirtualHost> 使用指令，服务器将以相同的方式处理所有接受的请求。但是， <VirtualHost> 可用于为一个或多个地址或端口指定不同的行为。要实现 VirtualHost，必须首先告诉服务器侦听要使用的地址和端口。然后应该为指定的地址和端口创建一个 <VirtualHost>部分来设置该虚拟主机的行为。注意，如果 <VirtualHost> 设置了服务器没有监听的地址和端口，则无法访问。

```
Listen [IP-address:]portnumber [protocol]		  
  IP addresses and ports that the server listens to

ServerName [scheme://]domain-name|ip-address[:port]		
  Hostname and port that the server uses to identify itself

DocumentRoot directory-path  	
  Directory that forms the main document tree visible from the web

DirectoryIndex disabled | local-url [local-url] ...	
  List of resources to look for when the client requests a directory

<VirtualHost addr[:port] [addr[:port]] ...> ... </VirtualHost>		
  Contains directives that apply only to a specific hostname or IP address
```

main server  (监听地址和端口 默认的请求处理行为) 
virtual hosts  (对特定的地址和端口 设置特定的请求处理行为，虚拟主机设置的地址和端口，需要是主服务器正在监听的才会生效)

The Listen directive does not implement Virtual Hosts - it only tells the main server what addresses and ports to listen on. If no <VirtualHost> directives are used, the server will behave in the same way for all accepted requests. However, <VirtualHost> can be used to specify a different behavior for one or more of the addresses or ports. To implement a VirtualHost, the server must first be told to listen to the address and port to be used. Then a <VirtualHost> section should be created for the specified address and port to set the behavior of this virtual host. Note that if the <VirtualHost> is set for an address and port that the server is not listening to, it cannot be accessed.

https://httpd.apache.org/docs/2.4/bind.html

指令不区分大小写 推荐使用驼峰命名法 但是指令参数通常是区分大小写的

Directives in the configuration files are case-insensitive, but arguments to directives are often case sensitive. 

https://httpd.apache.org/docs/2.4/configuring.html

## VMWare 禁用共享虚拟机 解决 443 端口被占用问题

启动 VMWare -> 编辑 -> 首选项 -> 共享虚拟机 -> 禁用

![01.png](Apache/01.png)
