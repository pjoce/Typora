下面教程非常详细，大家一步步对照视频都可以把服务跑起来。

搭建环境大家会遇到很多坑，程序汪都总结了



核心步骤

1. 阿里云准备 centos_8_0，安全组设置（可以虚拟机代替）
2. linux下jdk maven git 配置
3. 添加SSH公钥从gitee克隆源代码
4. liunx下docker安装
5. docker下mysql安装
6. git拉取项目代码
7. 执行项目中sql的数据库脚本
8. 修改项目配置并启动





![图片](https://mmbiz.qpic.cn/mmbiz_png/NM4CSPVMwEvmicpruX6h8iae5KVkyuk3zq8vG1hicLQ7VMl2OGkeT8Wsl0xdeRXAauILNOiaLSgHbPjIBWEeQEwqKQ/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)





**环境部署**

**准备工作**

- 阿里云

  centos_8_0_x64_20G_alibase_20200218.vhd

[root@iZ2zeeqw5fxmm9zagf439aZ ~]# cat /etc/redhat-release

CentOS Linux release 8.1.1911 (Core) 

[root@iZ2zeeqw5fxmm9zagf439aZ ~]# 

注意阿里云需要安全组端口设置，不然无法访问

![图片](https://mmbiz.qpic.cn/mmbiz_png/NM4CSPVMwEvmicpruX6h8iae5KVkyuk3zqQVDJvnSXXkMX167co8gWLmTMswXbY05XmRwfTgzTomouZKibfpWGWBA/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)



以上目录非必须，可以自行选择安装路径。

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

  ```
  #进入安装包目录，解压
  tar -zxvf apache-maven-3.6.3-bin.tar.gz
  
  #修改环境变量/etc/profile，MAVEN_HOME修改为自己的jdk路径。
  export MAVEN_HOME=/data/service/maven-3.6.3
  export PATH=$PATH:$MAVEN_HOME/bin
  
  #使环境变量生效
  source /etc/profile
  
  #检查是否配置成功
  mvn -v
  ```

- Git

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

- Mysql >= 5.5.0 (推荐5.7版本)

  使用docker的方式安装mysql，首先安装docker

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
  docker run -p 3306:3306 --name mysql -v /data/docker/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
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

  

  注意3：如果使用docker进入mysql数据库执行包含中文的sql语句，出现中文乱码的情况，需要在容器系统中设置使用中文包。所以进入docker容器时需要设置环境语言：

  注意2：在mysql的镜像启动之前，创建配置文件目录，并放置好配置文件。这样mysql启动的时候，就可以直接使用配置好的参数。以下内容是配置数据库的字符集为utf-8。

  注意1：MySQL(5.7.19)的默认配置文件是 /etc/mysql/my.cnf 文件。如果想要自定义配置，建议向 /etc/mysql/conf.d 目录中创建 .cnf 文件。新建的文件可以任意起名，只要保证后缀名是 cnf 即可。

  在Docker中安装MySQL。

  注意：如果安装docker-ce报错，可以执行以下语句，再进行安装。

- 安装之后，在git仓库中需要添加SSH公钥

### 项目部署

**部署步骤**

1. 从git仓库clone下来最新代码。ruoyi项目的gitee地址是https://gitee.com/y_project/RuoYi

```
#创建git仓库目录，clone项目源代码
mkdir -p /data/gitee
cd /data/gitee
git clone git@gitee.com:y_project/RuoYi.git
#复制需要执行的sql文件到docker的映射目录中
cp /data/gitee/RuoYi/sql/quartz.sql /data/docker/mysql/conf/
cp /data/gitee/RuoYi/sql/ry_20200911.sql /data/docker/mysql/conf/

#进入docker容器中，执行sql文件
docker exec -it mysql env LANG=C.UTF-8 bash
#连接mysql
mysql -uroot -p
#创建数据库ruoyi
create database if not exists ruoyi character set='utf8';
#进入数据库，执行文件
use ruoyi;
source /etc/mysql/conf.d/quartz.sql;
source /etc/mysql/conf.d/ry_20200911.sql;
cd /data/gitee/RuoYi/ruoyi-admin/src/main/resources/
#修改application.yml的项目启动端口号
vim application.yml
#修改application-druid.yml的数据源
vim application.yml
server:
port: 18081

ruoyi:
profile: /data/tmp
# 数据源配置
spring:
  datasource:
      type: com.alibaba.druid.pool.DruidDataSource
      driverClassName: com.mysql.cj.jdbc.Driver
      druid:
          # 主库数据源
          master:
              url: jdbc:mysql://localhost:3306/ruoyi?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8
              username: root
              password: 123456
cd /data/gitee/RuoYi/
mvn install
```

1. 启动项目：把jar复制到程序目录，通过java命令行的方式启动jar包文件。因为本项目是使用了spring boot框架，所以jar中内置了web容器，不需要额外安装tomcat了。

   ```
   mkdir -p /data/app/ruoyi-admin
   cp /data/gitee/RuoYi/ruoyi-admin/target/ruoyi-admin.jar /data/app/ruoyi-admin/ruoyi-admin.jar
   cd /data/app/ruoyi-admin/
   nohup java -jar ruoyi-admin.jar &
   ```

   nohup和&用来表示本命令需要在后台执行，这样退出命令行程序后，java程序也不会停止。

2. 1. 访问项目的地址，验证项目是否成功运行。

      本教程中设置的端口是18081，所以可以通过http://服务器公网IP:18081的方式进行访问，如果是在本地启动的程序，则直接访问http://localhost:18081即可。

      本演示教程的访问地址是：http://123.56.41.4:18081

      注意：如果是使用了阿里云/腾讯云/华为云这种的公有云平台，如果想通过http://服务器公网IP:18081的方式进行访问，需要在这些公有云后台开放相应端口的权限。具体的操作步骤请查看对应服务商的官方文档。

      腾讯云：https://cloud.tencent.com/document/product/213/34601

      阿里云：https://yq.aliyun.com/articles/701181

打包完成后，/data/gitee/RuoYi/ruoyi-admin/target/ruoyi-admin.jar就是打包完成后的jar包文件。

1. 修改好项目的配置文件之后，把入口项目进行打包。通过mvn打包成一个可执行的jar包。

修改application-druid.yml的数据源

修改application.yml的项目启动端口号，修改为18081。修改上传文件路径为/data/tmp

1. 修改项目中的配置文件，配置文件有两个，分别是application.yml和application-druid.yml。

1. 在本地数据库中执行项目中的SQL语句。本项目中的sql语句是存放在了项目的sql文件夹中，也就是/data/gitee/RuoYi/sql/中。

注意：在clone代码之前，需要在gitee管理后台中添加服务器的SSH公钥。详见https://gitee.com/help/articles/4191