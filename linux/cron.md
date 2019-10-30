### 周期性计划作业--cron

cron:是一个进程。可以让linux周期性的执行某一命令

crontab是一个命令，可以设置linux周期性的执行某一命令。
```
-u  ：设置某一个用户的周期性工作—root权限；
-e  ：编辑 crontab 的工作内容
-l  ：查阅 crontab 的工作内容
-r  ：移除所有的 crontab 的工作内容，若仅要移除一项，请用 -e 去编辑
```

### crontab的格式讲解

一行代表一个任务
minute   hour   day   month   week   command
```
minute： 表示分钟，可以是从0到59之间的任何整数。
hour：表示小时，可以是从0到23之间的任何整数。
day：表示日期，可以是从1到31之间的任何整数。
month：表示月份，可以是从1到12之间的任何整数。
week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。
```

实例
```
* * * * * echo "hello" #每1分钟执行hello
3,15 * * * * command #每小时第三分钟和第五分钟执行
3,15 8-11 * * * command# 在上午8点到11点的第3和第15分钟执行
3,15 8-11 */2  *  * command #每隔两天的上午8点到11点的第3和第15分钟执行
30 23 * * * /etc/init.d/nginx restart #每晚的23:30重启nginx
0 23 * * 6 /etc/init.d/smb restart #每星期六的晚上11 : 00 pm重启smb
20,40 7-11 */1 * * command 每隔一天的7点到11点的第20和第40分钟执行
25,50 7-11 * * 1 command  每周一的7点到11点的第25和第50分钟执行
```

注意事项

新创建的cron job，不会马上执行，至少要过2分钟才执行。如果重启cron则马上执行。

当crontab失效时，可以尝试/etc/init.d/crond restart解决问题。或者查看日志看某个job有没有执行/报错tail -f /var/log/cron。
```bash
service cron restart
```