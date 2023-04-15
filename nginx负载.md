**负载均衡**

**负载均衡介绍**

Load balancing，即负载均衡，是一种计算机技术，用来在多个计算机（计算机集群）、网络连接、CPU、磁盘驱动器或其他资源中分配负载，以达到最优化资源使用、最大化吞吐率、最小化响应时间、同时避免过载的目的。

负载均衡分为**硬件负载均衡**和**软件负载均衡**。

硬件负载均衡解决方案是直接在服务器和外部网络间安装负载均衡设备，这种设备我们通常称之为负载均衡器，负载均衡器产品有F5、NetScaler等。

软件负载均衡解决方案是指在一台或多台服务器相应的操作系统上安装一个或多个附加软件来实现负载均衡，它的优点是基于特定环境，配置简单，使用灵活，成本低廉，可以满足一般的负载均衡需求。目前比较流行的就三类软件负载均衡，LVS、Nginx和HAProxy。用的最多的还是LVS和Nginx这两种。

本次就以Nginx为例来演示。

**准备工作**

1.安装nginx

```
#首先安装nginx前置软件yum -y install gcc pcre-devel zlib-devel openssl openssl-devel#下载nginx软件安装包cd /data/tmpwget http://nginx.org/download/nginx-1.18.0.tar.gztar -zxvf nginx-1.18.0.tar.gzcd nginx-1.18.0#设置安装目录为/usr/local/nginx./configure --prefix=/usr/local/nginxmakemake install#启动nginx/usr/local/nginx/sbin/nginx
```

2.通过修改端口号的方式，启动多个程序副本。启动程序时加上server.port参数来指定不同的端口。

```
cd /data/app/ruoyi-adminmkdir 18081 18082 18083cp /data/app/ruoyi-admin/ruoyi-admin.jar /data/app/ruoyi-admin/18081/ruoyi-admin.jarcp /data/app/ruoyi-admin/ruoyi-admin.jar /data/app/ruoyi-admin/18082/ruoyi-admin.jarcp /data/app/ruoyi-admin/ruoyi-admin.jar /data/app/ruoyi-admin/18083/ruoyi-admin.jarcd /data/app/ruoyi-admin/18081/nohup java -jar ruoyi-admin.jar --server.port=18081 &cd /data/app/ruoyi-admin/18082/nohup java -jar ruoyi-admin.jar --server.port=18082 &cd /data/app/ruoyi-admin/18083/nohup java -jar ruoyi-admin.jar --server.port=18083 &
```

3.修改nginx配置文件，进行负载均衡配置。

```
vim /usr/local/nginx/conf/nginx.conf
```

设置负载均衡方式，默认是轮询方式。本示例选择了iphash，每个请求都根据访问ip的hash结果分配，经过这样的处理，每个访客固定访问一个后端服务。这样可一定程度避免多后端服务下Session同步的问题，但是还是建议使用Spring Session来解决Session共享问题。

```
upstream ruoyi-server {
ip_hash;
    server   localhost:18081;
    server   localhost:18082;
server   localhost:18083;
}
```

配置代理。

```
location / {
  proxy_pass   http://ruoyi-server;
  proxy_set_header Host $http_host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
}
```

重启nginx。

```
/usr/local/nginx/sbin/nginx -s reload
```



演示一下负载均衡效果：

1）ip_hash：依据ip分配方式，指定负载均衡器按照基于客户端IP的分配方式，这个方法确保了相同的客户端请求一致发送到相同的服务器，以保证session会话。这样每个访客都固定访问一个后端服务器，可以解决session不能跨服务器的问题。

```
upstream [服务器组名称]{
  ip_hash;
　　server [IP地址]:[端口号];
　　server [IP地址]:[端口号];
}
```

2）轮询：最基本的配置方法，是upstream模块默认的负载均衡策略。每个请求会按时间顺序平均分配到不同的后端服务器。

```
upstream [服务器组名称]{
　　server [IP地址]:[端口号];
　　server [IP地址]:[端口号];
}
```

3）weight：权重方式，在轮询策略的基础上指定轮询的几率。也可以认为是在轮询的基础上新增了一个weight的参数，此参数指定轮询的几率，值为number。

```
upstream [服务器组名称]{
　　server [IP地址]:[端口号] weight=2;
　　server [IP地址]:[端口号] weight=8;
}
```