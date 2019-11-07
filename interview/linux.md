## linux 常见面试题

### 命令

1.查看某个进程的信息

    ps -p PID
    ls /proc/PID

2.查找文件大于5M的

    查找大于5M的文件
    find . -type f -size +5m 
    查找小于5M的文件
    find . -type f -size -5m

### 文件结构

1.硬链接和软连接区别

    硬链接(Hard Link)多个文件名指向同一索引节点(Inode),计数器+1
    ln /usr/local/nginx/sbin/nginx  /usr/local/bin/nginx

    软连接(Symbolic Link)inode节点的数据项保存原文件路径
    ln -s /usr/local/nginx/sbin/nginx /usr/local/bin/nginx

### 