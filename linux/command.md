帮助命令 `ls --help`

# 文件与目录管理

linux中一切皆为文件

使用ll或者ls –l命令来显示一个文件的属性以及文件所属的用户和组

```bash
[root@www /]# ls -l
total 64
dr-xr-xr-x   2 root root 4096 Dec 14  2012 bin
dr-xr-xr-x   4 root root 4096 Apr 19  2012 boot
```
bin文件的第一个属性用"d"表示。"d"在Linux中代表该文件是一个目录文件。
在Linux中第一个字符代表这个文件是目录、文件或链接文件等等。
- [ d ]则是目录
- [ - ]则是文件；
- [ l ]则表示为链接文档(link file)；
- [ b ]则表示为装置文件里面的可供储存的接口设备(可随机存取装置)；
- [ c ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标(一次性读取装置)。

### 更改文件属性
1、chgrp：更改文件属组 `chgrp [-R] 属组名 文件名` -R：递归更改文件属组

2、chown：更改文件属主，也可以同时更改文件属组
- `chown [–R] 属主名 文件名`
- `chown [-R] 属主名：属组名 文件名`

3、chmod：更改文件9个属性  -R：递归更改文件属组
- `chmod [-R] 777 文件或目录`

chmod u/g/o/a +/-/= rwx 文件名

将文件权限设置为 -rwxr-xr-- ，可以使用 chmod u=rwx,g=rx,o=r 文件名


### 创建和删除
- mkdir：创建一个新的目录
- rmdir：删除一个空的目录
- pwd：显示目前的目录
- touch: 创建文件 
- rm: 删除文件（删除非空目录：rm -rf file目录）
- cp：复制 (复制目录：cp -r )
- mv: 移动

### 查看文件 `ls`
选项与参数：
- -a ：全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来
- -d ：仅列出目录本身，而不是列出目录内的文件数据
- -l ：长数据串列出，包含文件的属性与权限等等数据

查看文件内容 `cat` 可以加more 、less控制输出
```bash
cat a.text
cat a.text | more
cat a.text| less
```

### 管道符
字符：｜。这就是管道符。

作用有两个：
1、承上启下：把上一个指令的输出作为下一个指令的输入来执行。
2、搭配grep 字符实现过滤功能。
```bash
[root@localhost tmp]# ls -l /etc/ | more
[root@localhost tmp]# ps -aux | grep cron
[root@localhost tmp]# ps -aux | grep cron > abc
```


### 输入输出重定向
- \> file 	 //标准输出重定向到文件—覆盖
- \>\>file 	//标准输出重定向到文件—追加
- 2>		//标准错误重定向到文件—覆盖		
- 2>>		//标准错误重定向到文件—追加
- 2>/dev/null	 //标准错误重定向到回收站
- &>file	//标准输出和标准错误重定向到文件—覆盖
- \>\>file 2>&1	//标准输出和标准错误重定向到文件—追加


### 文本处理

### 查找和替换
- `grep` 只能用于查找文件中的内容
- `sed` 可以查找，然后替换或者插入想要的内容
main grep, main sed

```text
a ：新增，a的后面可以接字串,而这些字串会在新的一行出现(目前的下一行)；
d ：删除，因为是删除啊，所以d后面通常不接任何东西的；
i ：插入，i的后面可以接字串,而这些字串会在新的一行出现(目前的上一行)；
p ：列印，亦即将某个选择的资料印出。通常 p 会与参数 sed
s ：替换，可以直接进行取代的工作！
```

```text
sed [options] 'command' file(s)
首处替换
sed 's/text/replace_text/' file   //替换每一行的第一处匹配的text
全局替换
sed 's/text/replace_text/g' file
默认替换后，输出替换后的内容，如果需要直接替换原文件,使用-i:
sed -i 's/text/repalce_text/g' file
移除空白行
sed '/^$/d' file
sed 's/book/books/' file #替换文本中的字符串：
sed 's/book/books/g' file
sed '/^$/d' file #删除空白行

删除/tmp/abc文件中第 2 至 5 行的内容
sed '2,5d' /temp/abc
在第 2 行后面添加 123456 这一行
sed '2a 123456' /temp/abc
在文件的最后一行前面添加 123456
sed '$i 123456' /temp/abc
将文件中的 cat全部替换成 dog
sed 's/cat/dog' /temp/abc
如果要对原文件进行修改，使用 –i 选项（慎用）
```

### 文件查找 `find`
用法：find [路径] [命令参数] [表达式]
参数：
- -name 文件名：查找指定名称文件
- -user 用户名：查找指定用户拥有的文件
- -group 组名：查找指定组拥有的文件
- -mtime n：查找在N 天前被修改过的文件
- -atime n：查找在N 天前被访问过的文件
- -type d/f/b/l/p：查找指定类型的文件
- -empty：查找为空的文件
- -size：按容量大小查找
- -perm mode:查找指定属性的文件
- -exec command {} \;：查找指定的文件并执行指定的命令
- -newer 文件名 ：查找比指定文件新的文件

```bash
find . -atime 7 -type f -print
find . -type d -print  //只列出所有目录
find / -name "hello.php" 查找hello.php文件
```

### 数据流处理awk
```bash
awk ' BEGIN{ statements } statements2 END{ statements } '
```
特殊变量

NR:表示记录数量，在执行过程中对应当前行号

NF:表示字段数量，在执行过程总对应当前行的字段数

$0:这个变量包含执行过程中当前行的文本内容

$1:第一个字段的文本内容

$2:第二个字段的文本内容

```bash
awk '{print $2, $3}' file
# 日志格式：'$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" "$http_x_forwarded_for"'
#统计日志中访问最多的10个IP
awk '{a[$1]++}END{for(i in a)print a[i],i|"sort -k1 -nr|head -n10"}' access.log

# 查看php-fpm意外终止原因
ps -ef|grep php-fpm|awk '{print " -p " $2" -s 10000 -o /tmp/strace_"$2".log"}'|xargs strace

```
nginx日志分析

日志格式
```
'$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" "$http_x_forwarded_for"'
```
- 统计日志最多的10个IP
```
awk '{arr[$1]++} END {for(i in arr) {print arr[i]}}' access.log | sort -k1 -nr | head -n10
```
- 统计日志访问次数大于100次的IP
```
awk '{arr[$1]++} END{for (i in arr) {if(arr[i] > 100){print $i}}}' access.log
```
- 统计2018年8月1日内访问最多的10个ip
```
awk '$4>="[01/Aug/2018:00:00:00" && $4<="[01/Aug/2018:23:59:59" {arr[i]++} END{print arr[i]}' |sort -k1 -nr|head -n10
```
- 统计访问最多的十个页面
```
 awk '{a[$7]++}END{for(i in a)print a[i],i|"sort -k1 -nr|head -n10"}' access.log
 ```
- 统计访问状态为404的ip出现的次数
 ```
 awk '{if($9~/404/)a[$1" "$9]++}END{for(i in a)print i,a[i]}' access.log
 ```

### Xargs

读取输入数据重新格式化后输出

默认命令是 echo，空格是默认定界符。这意味着通过管道传递给 xargs 的输入将会包含换行和空白，不过通过 xargs 的处理，换行和空白将被空格取代。

```
cat test.txt | xargs

a b c d e f g h i j k l m n o p q r s t u v w x y z
```


### 排序 `port`
- -n 按数字进行排序 VS -d 按字典序进行排序
- -r 逆序排序
- -k N 指定按第N列排序
```bash
sort -nrk 1 data.txt
sort -bd data // 忽略像空格之类的前导空白字符
```

### 去重`uniq`
- 消除重复行
```bash
sort unsort.txt | uniq
```

### 统计 `wc`
```bash
wc -l file // 统计行数
wc -w file // 统计单词数
wc -c file // 统计字符数
```

### which

查询运行文件的所在路径

```
which php
/usr/local/bin/php
```

### whereis

输出文件安装的目录

```
which nginx
nginx: /usr/sbin/nginx /etc/nginx /usr/share/nginx
```

### rpm安装
rpm -ivh nginx
rpm -ql nginx



> [Linux 命令大全](http://www.runoob.com/linux/linux-command-manual.html)

http://man.linuxde.net