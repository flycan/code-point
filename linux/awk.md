
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

https://segmentfault.com/a/1190000009745139