**系统环境需求**

```
阿里云CentOS8.2
JDK >= 1.8
MySQL >= 5.7
Maven >= 3.0
Redis
Node.js
Nginx
```

- 新建目录

  ```
  #tmp存放临时安装包
  mkdir -p /data/tmp
  #service存放软件环境
  mkdir -p /data/service
  #gitee存放代码版本控制库
  mkdir -p /data/gitee
  ```

  以上目录非必须，可以自行选择安装路径。

- JDK >= 1.8 (推荐1.8版本)

  从oracle官方网站上下载1.8版本中的最新版的JDK。下载完成后，把文件通过WinSCP或者XFTP上传到服务器上。接着进行解压和配置环境变量。

  下载地址：https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

  ```
  #进入安装包目录，解压
  cd /data/tmp
  tar -zxvf jdk-8u261-linux-x64.tar.gz
  
  #把解压出来的文件夹转移到统一的地方
  mv /data/tmp/jdk1.8.0_261 /data/service/jdk1.8.0_261
  
  #修改环境变量/etc/profile，JAVA_HOME修改为自己的jdk路径。
  vim /etc/profile
  export JAVA_HOME=/data/service/jdk1.8.0_261
  export PATH=$PATH:$JAVA_HOME/bin
  
  #使环境变量生效
  source /etc/profile
  
  #检查是否配置成功
  java -version
  ```

- Maven >= 3.0

  从Maven官方网站上下载最新版的压缩包。下载完成后，进行解压和配置环境变量。

  下载地址：https://maven.apache.org/download.cgi

  ```
  #进入安装包目录，解压
  cd /data/tmp
  tar -zxvf apache-maven-3.6.3-bin.tar.gz
  mv apache-maven-3.6.3 /data/service/
  
  #修改环境变量/etc/profile，MAVEN_HOME修改为自己的路径。
  export MAVEN_HOME=/data/service/apache-maven-3.6.3
  export PATH=$PATH:$MAVEN_HOME/bin
  
  #使环境变量生效
  source /etc/profile
  
  #检查是否配置成功
  mvn -v
  ```

- Git

  从git官方网站上下载最新版的安装包，安装即可。

  下载地址：https://git-scm.com/

  ```
  #通过yum安装，本方法最简单。但是无法控制安装的版本
  yum -y install git
  
  #检查是否配置成功
  git --version
  ```

  ```
  ssh-keygen -t rsa -C "xx@xx.com"
  cat ~/.ssh/id_rsa.pub
  ```

- 安装docker

  ```
  #卸载旧版本
  sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
  #安装 Docker Engine-Community
  sudo yum install -y yum-utils device-mapper-persistent-data lvm2
  sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  sudo yum install docker-ce docker-ce-cli containerd.io
  sudo systemctl start docker
  ```

  ```
  yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
  ```

- Mysql >= 5.7 (注意生产项目mysql都是独立部署)

  安装教程：https://www.runoob.com/mysql/mysql-install.html

  下载地址：https://dev.mysql.com/downloads/mysql/

  访问Mysql的可视化数据库开发工具：Navicat Premium 工具的安装教程可自行百度搜索。	

  在Docker中安装MySQL。

  ```
  #查看可用的 MySQL 版本
  docker search mysql
  #拉取5.7版本
  docker pull mysql:5.7
  #创建配置文件目录
  mkdir -p /data/docker/mysql/conf
  #进入配置文件目录，添加一个配置文件
  vim my.cnf
  #启动镜像
  docker run -p 3306:3306 --name mysql -v /data/docker/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=Vv0IBQ5qST -d mysql:5.7
  ```

  ```
  [mysqld]
  character-set-server=utf8
  
  [mysql]
  default-character-set=utf8
  ```

  ```
  #进入docker容器设置env LANG=C.UTF-8
  docker exec -it mysql env LANG=C.UTF-8 bash
  ```

- Redis

  安装教程：https://www.runoob.com/redis/redis-install.html

  下载地址：https://github.com/tporadowski/redis/releases

  Redis可视化管理工具：[AnotherRedisDesktopManager]:(https://github.com/qishibo/AnotherRedisDesktopManager/releases)

  ```
  docker pull redis:latest
  docker run -itd --name redis-test -p 6379:6379 redis
  ```

- Node.js

  安装教程：https://www.runoob.com/nodejs/nodejs-install-setup.html

  ```
  tar xf node-v14.15.4-linux-x64.tar.xz       // 解压
  cd node-v14.15.4-linux-x64/                 // 进入解压目录
  ./bin/node -v                               // 执行node命令 查看版本
  
  //解压文件的 bin 目录底下包含了 node、npm 等命令，我们可以使用 ln 命令来设置软连接：
  mv /data/tmp/node-v14.15.4-linux-x64 /data/service/
  ln -s /data/service/node-v14.15.4-linux-x64/bin/npm   /usr/local/bin/
  ln -s /data/service/node-v14.15.4-linux-x64/bin/node   /usr/local/bin/
  ```

- Nginx 安装

  ```
  #首先安装nginx前置软件
  yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
  #下载nginx软件安装包
  cd /data/tmp
  wget http://nginx.org/download/nginx-1.19.6.tar.gz
  tar -zxvf nginx-1.19.6.tar.gz
  cd nginx-1.19.6
  #设置安装目录为/usr/local/nginx
  ./configure --prefix=/usr/local/nginx
  make
  make install
  #启动nginx
  /usr/local/nginx/sbin/nginx
  ```

  ```
  #clone项目源代码
  git clone git@gitee.com:y_project/RuoYi-Vue.git
  ```

  注意：在clone代码之前，需要在gitee管理后台中添加服务器的SSH公钥。详见https://gitee.com/help/articles/4191

  **后端部署步骤**

- 1. 从git仓库clone下来最新代码。ruoyi前后端分离版本的gitee地址是https://gitee.com/y_project/RuoYi-Vue

- 1. 修改项目中的配置文件，配置文件有两个，分别是application.yml和application-druid.yml。

- 1. 启动项目：把jar复制到程序目录，通过java命令行的方式启动jar包文件。因为本项目是使用了spring boot框架，所以jar中内置了web容器，不需要额外安装tomcat了。

     ```
     mkdir -p /data/app/ruoyi-vue
     cp /data/gitee/RuoYi-Vue/ruoyi-admin/target/ruoyi-admin.jar /data/app/ruoyi-vue/ruoyi-admin.jar
     cd /data/app/ruoyi-vue/
     nohup java -jar ruoyi-admin.jar &
     ```

     nohup和&用来表示本命令需要在后台执行，这样退出命令行程序后，java程序也不会停止。

- 1. 查看日志，验证项目是否成功运行。

     

- 1. 修改好项目的配置文件之后，把入口项目进行打包。通过mvn打包成一个可执行的jar包。

- 1. 使用可视化数据库开发工具 Navicat Premium进入数据库管理界面。创建数据库`ry-vue`并执行数据脚本`ry_2021xxxx.sql`，`quartz.sql`

     ```
     cd /data/gitee/RuoYi-Vue/ruoyi-admin/src/main/resources/
     #修改application.yml的项目启动端口号
     vim application.yml
     #修改application-druid.yml的数据源
     vim application.yml
     ```

     ```
     server:
     port: 18081
     
     ruoyi:
     profile: /data/tmp
     
     # redis 配置
     spring:
     redis:
           # 地址
           host: localhost
           # 端口，默认为6379
           port: 6379
           # 数据库索引
           database: 0
           # 密码
           password:
     ```

     ```
     # 数据源配置
     spring:
       datasource:
           type: com.alibaba.druid.pool.DruidDataSource
           driverClassName: com.mysql.cj.jdbc.Driver
           druid:
               # 主库数据源
               master:
                   url: jdbc:mysql://localhost:3306/ry-vue?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8
                   username: root
                   password: Vv0IBQ5qST
     ```

     ```
     cd /data/gitee/RuoYi-Vue/
     mvn clean install -pl com.ruoyi:ruoyi-admin -am
     ```

     ```
     # 进入项目目录
     cd /data/gitee/RuoYi-Vue/ruoyi-ui
     
     #本地启动的话，可以在这里修改后端接口端口。如果是直接打包的话，不需要修改该配置文件
     vim vue.config.js
     
     # 打包正式环境
     npm run build:prod
     
     mkdir /data/app/ruoyi-ui
     mv dist/* /data/app/ruoyi-ui/
     
     #把生成好的 dist 文件夹的静态文件发布到你的 nginx 或者静态服务器即可，其中的 index.html 是后台服务的入口页面。
     vim /usr/local/nginx/conf/nginx.conf
     
     location / {
               root   /data/app/ruoyi-ui;
     try_files $uri $uri/ /index.html;
               index index.html index.htm;
           }
            
     location /prod-api/ {
     proxy_set_header Host $http_host;
     proxy_set_header X-Real-IP $remote_addr;
     proxy_set_header REMOTE-HOST $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_pass http://localhost:18081/;
     }
     
     #重新加载配置文件nginx
     /usr/local/nginx/sbin/nginx -s reload
     ```

     提示：node版本需要使用最新的稳定版本，v14.15.4之前的版本可能会打包失败。

     打开浏览器，输入：http://localhost（默认账户/密码 `admin/admin123`）若能正确展示登录页面，并能成功登录，菜单及页面展示正常，则表明环境搭建成功。

     **前端部署步骤**

     打包完成后，/data/gitee/RuoYi-Vue/ruoyi-admin/target/ruoyi-admin.jar就是打包完成后的jar包文件。

     修改application-druid.yml的数据源

     修改application.yml的项目启动端口号，修改为18081。修改上传文件路径为/data/tmp

- 注意3：如果使用docker进入mysql数据库执行包含中文的sql语句，出现中文乱码的情况，需要在容器系统中设置使用中文包。所以进入docker容器时需要设置环境语言：

- 注意2：在mysql的镜像启动之前，创建配置文件目录，并放置好配置文件。这样mysql启动的时候，就可以直接使用配置好的参数。以下内容是配置数据库的字符集为utf-8。

- 注意1：MySQL(5.7.19)的默认配置文件是 /etc/mysql/my.cnf 文件。如果想要自定义配置，建议向 /etc/mysql/conf.d 目录中创建 .cnf 文件。新建的文件可以任意起名，只要保证后缀名是 cnf 即可。

- 注意：如果安装docker-ce报错，可以执行以下语句，再进行安装。

- 安装之后，在git仓库中需要添加SSH公钥