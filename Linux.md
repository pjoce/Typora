```
// Linux下配置 Java开发环境
// by JAVA2022高级 linux 百度云
安装JDK
1. 使用FinalShell自带的上传工具 将jdk的二进制发布包上传

2. 解压安装包， 命令为tar -zxvf jdk-8u171-linux-x64.tar.gz -C /usr/local

3.配置环境变量， 使用vim命令修改/etc/profile文件，在文件末尾加入如下配置
JAVA_HOME=/usr/local/jdk1.8.0_171
PATH=$JAVA_HOME/bin:$PATH

4.重新加载profile文件，使更改的配置立即生效，命令为source /etc/profile

5.检查安装是否成功， java -version

安装Tomcat
步骤： FinalShell上传，解压命令为 tar -zxvf apache-tomcat-7.0.57.tar.gz -C /usr/local

mv apache-tomcat-7.0.57/ tomcat7 修改文件名

进入Tomcat的bin目录启动服务， sh startup.sh 或 ./startup.sh

查看进程  ps -ef | grep tomcat 

ps -ef 可以查看当前允许的所有进程的详细信息
“|" 在linux中成为管道符， 可以将前一个命令结果输出给后一个命令作为输入
动态查看 tomcat 日志 
tail -f catalina.out






```

