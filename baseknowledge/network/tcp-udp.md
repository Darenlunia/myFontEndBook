---
description: 从以前笔记中粘贴过来的，格式不太优美，有空整理。
---

# TCP/UDP

Chrome 在同一个域名下要求同时最多只能有 6 个 TCP 连接，超过 6 个的话剩下的请求就得等待。等待后才进入 TCP 连接的建立阶段。首先解释一下什么是 TCP:

> TCP（Transmission Control Protocol，传输控制协议）是一种面向连接的、可靠的、基于字节流的传输层通信协议。

* TCP和UDP的区别？[http://47.98.159.95/my\_blog/tcp/001.html](http://47.98.159.95/my_blog/tcp/001.html)
* 为什么TCP三次握手，而不是两次或四次？
  * [http://47.98.159.95/my\_blog/tcp/002.html\#恋爱模拟](http://47.98.159.95/my_blog/tcp/002.html#恋爱模拟)
  * 对应到 TCP 的三次握手，也是需要确认双方的两样能力: 发送的能力和接收的能力。
* 为什么是四次挥手而不是三次挥手？
  * [http://47.98.159.95/my\_blog/tcp/003.html\#过程拆解](http://47.98.159.95/my_blog/tcp/003.html#过程拆解)
* MSL/RTT/RTO/TTL
  * MSL：最大报文生存时间 Maximum Segment Lifetime（热和保温在网络上生存的最长时间）（tcp报文：segment）（ip数据报：datagram）（TCP四次挥手时用到了2\*MSL）
  * RTT：客户到服务器往返所花事件Round-Trip time（由TCP动态估算）（不是一个固定值，是通过数据传输计算的）
  * RTO：超时重传时间，Retranmission TimeOut（对应于RTT） （重传间隔不是一个固定值，有计算方法，是由RTT决定的）
  * TTL：生存时间 Time to live（由源主机设置初始值，但不存具体时间，存储的是一个IP数据报可经过的最大路由数）
* TCP的流量怎么控制的？（滑动窗口）
  * 在三次握手和数据传输过程中，随时协调和通知着双方的窗口大小。
* TCP的拥塞控制是怎么控制的？（拥塞窗口-限制发送窗口）
  * 拥塞窗口
  * 慢启动阈值（阈值是变化的）
  * 他这里的慢启动解释的有点问题，可以看这个文章[https://www.cnblogs.com/bincoding/p/8976157.html](https://www.cnblogs.com/bincoding/p/8976157.html)
  * 拥塞避免
  * 快速重传和快速恢复
  * 实例：
  * 1.TCP连接进行初始化的时候，cwnd=1,ssthresh=16。
  * 2.在慢启动算法开始时，cwnd的初始值是1，每次发送方收到一个ACK拥塞窗口就增加1，当ssthresh =cwnd时，就启动拥塞控制算法，拥塞窗口按照规律增长，
  * 3.当cwnd=24时，网络出现超时，发送方收不到确认ACK，此时设置ssthresh=12,\(二分之一cwnd\),设置cwnd=1,然后开始慢启动算法，当cwnd=ssthresh=12,慢启动算法变为拥塞控制算法，cwnd按照线性的速度进行增长。
* TCP/UDP的报文头有哪些内容
  * [https://blog.csdn.net/zuochao\_2013/article/details/80561793](https://blog.csdn.net/zuochao_2013/article/details/80561793)
  * 各字段含义：[http://47.98.159.95/my\_blog/tcp/005.html\#源端口](http://47.98.159.95/my_blog/tcp/005.html#源端口)、目标端口
* 一次TCP传输过程详解
  * [https://www.cnblogs.com/chenhuan001/p/7475754.html](https://www.cnblogs.com/chenhuan001/p/7475754.html)
  * **重要标志位：SYN** - 创建一个连接；**FIN** -  终结一个连接；**ACK** - 确认接收到的数据
  * 看完这个过程明白了一个很重要的道理：就是seq序列号和ack确认号什么时候加值。原则：序列号seq表示己方已经发送成功的数据位数（即已经收到ack反馈），确认号ack表示对方已经成功接收的数据位数。其中，每次发送的SYN标志位（只有三次握手时出现了两次）和FIN标志位（只有四次挥手时出现了两次）也要占序列号中的一位，ACK不占序列号中的位。中间传输数据的过程只有ACK标志位以及双方各自的确认号和序列号。

