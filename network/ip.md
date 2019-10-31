## IP协议

> IP协议是TCP/IP协议簇中的核心协议，也是TCP/IP的载体。所有的TCP，UDP，ICMP及IGMP数据都以IP数据报格式传输。 IP提供**不可靠**的，**无连接**的数据传送服务。 不可靠指它不能保证IP数据报能成功到达目的地 

不可靠（unreliable）的意思是它不能保证IP数据报能成功地到达目的地。IP仅提供最好的传输服务。如果发生某种错误时，如某个路由器暂时用完了缓冲区，IP有一个简单的错误处理算法：丢弃该数据报，然后发送ICMP消息报给信源端。任何要求的可靠性必须由上层来提供（如TCP）。

无连接（connectionless）这个术语的意思是IP并不维护任何关于后续数据报的状态信息。每个数据报的处理是相互独立的。这也说明，IP数据报可以不按发送顺序接收。如果一信源向相同的信宿发送两个连续的数据报（先是A，然后是B），每个数据报都是独立地进行路由选择，可能选择不同的路线，因此B可能在A到达之前先到达。

![img](http://blog.chinaunix.net/attachment/201304/27/26833883_1367053079KNJe.png)



## IP报文介绍

![(../MQ/images/52im_1.png)](http://docs.52im.net/extend/docs/book/tcpip/vol1/3/images2/52im_1.png) 

- 4位版本号。包含IP数据报的版本号：ipv4为4，ipv6为6 
- 4位首部长度
- 8位服务类型
- 16位总长度
- 标识字段：长度为16位，最多分配的ID值为65535个 

​	



### IP地址分类

IPV4被分为五大类：ABCDE

A类为：点分四组中的第一组地址范围为0~127的IP地址。已二进制来看就是“首位为0”

B类：128~191.二进制首位为10

C类：192~223.二进制首位为110

D类：224~239.二进制首位为1110

E类：240~255.二进制首位为1111


### 子网掩码

子网掩码(subnet mask)又叫网络掩码、地址掩码、子网络遮罩，它是一种用来指明一个IP地址的哪些位标识的是主机所在的子网，以及哪些位标识的是主机的位掩码。子网掩码不能单独存在，它必须结合IP地址一起使用。

子网掩码只有一个作用，就是将某个IP地址划分成**网络地址**和**主机地址**两部分

子网掩码是一个32位的2进制数， 其对应网络地址的所有位都置为1，对应于主机地址的所有位都置为0。子网掩码告知路由器，地址的哪一部分是网络地址，哪一部分是主机地址，使路由器正确判断任意IP地址是否是本网段的，从而正确地进行路由。只有网络标识相同的两台主机在无路由的情况下才能相互通信