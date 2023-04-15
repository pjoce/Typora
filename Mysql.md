```markdown
// the current server installed mysql

sc query mysql  查看mysql服务
sc delete mysql  删除该服务

 进入 C:\Program Files\MySQL\MySQL Server 8.0\bin 目录下
 初始化数据库 
 
mysqld --initialize --console 
执行完成后，会输出 root用户的初始默认密码

//安装命令
mysqld install

//启动命令

net start mysql

//默认密码 rvpdu,;v:3mW

//更改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';

// Navicat连接mysql失败出现报错：

authentication-plugin-caching-sha2-password-cannot-be-loaded

//解决步骤
0. mysql -u root -p

1.USE mysql;

2.ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'mypassword';

3.FLUSH PRIVILEGES;




```

