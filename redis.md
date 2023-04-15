```
打开 CMD， 进入  C:Program Files\Redis 下
从CMD 输入  redis-server.exe redis.conf
窗口保持开启状态
简单测试 redis
新建一个cmd 窗口，输入: ipconfig/all
查看本机的 ipv4地址
进入redis目录后输入下面命令: (-h 后面是自己的ip地址)
redis-cli.exe -h 192.xxx. -p 6379
连接成功后， 设置键值对: 
set key helloworld
获取key
get key

------------------------
4、把redis设置为windows的启动项


（D:\redis3.0 是我存放redis的目录，其实我的redis版本是2.4的并不是3.0的。。）

每次打开命令行启动Redis会很麻烦，把Redis设为windows启动项就不用每次都入命令行了 

4.1把命令写进一个.bat文件：在redis的目录下新建一个start.bat文件内容为

d:
cd redis3.0
redis-server.exe redis.conf
4.2利用vb脚本调用bat文件：再新建一个文件redis_run.vbs内容为

createobject（“wscript.shell”）.run "D:\redis3.0\start.bat",0
4.3把redis_run.vbs拖到windows启动里运行一下，关闭原来的redis启动cmd窗，在连接窗里输入 get key1 发现OK了，下次开机就会自动启动

或者：在cmd窗口打开注册表：regedit

在HKEY_CURRENT_USER \Software \Microsoft \Windows \CurrentVersion \Run 目录中新建字符串值

数值名称：opendis
数值数据："D:\redis3.0\redis_run.vbs" -min

确定，关闭，下次开机redis就可以自启了


```





