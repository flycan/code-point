## mysql连接1251的问题

1521-Client does not support authentication  protocol requested by server；

连接的加密方式已经改变

```mysql
> mysql -u root -p
> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';
> flush privileges;