# tcp&udp

> **参考**
>
> [《一文搞懂 TCP 与 UDP 的区别》(作者：Fundebug)【来源：博客园】](https://www.cnblogs.com/fundebug/p/differences-of-tcp-and-udp.html)
>
> [《TCP 和 UDP 的区别》(作者：陈宝佳)【来源：知乎】](https://zhuanlan.zhihu.com/p/24860273)
>
> [《TCP 与 UDP 的技术面试考点》(作者：青柚\_)【来源：CSDN】](https://blog.csdn.net/qq_38950316/article/details/81122894)

- TCP 是面向连接的，udp 是无连接的即发送数据前不需要先建立链接。
- TCP 提供可靠的服务。也就是说，通过 TCP 连接传送的数据，无差错，不丢失，不重复，且按序到达; UDP 尽最大努力交付，即不保证可靠交付。 并且因为 tcp 可靠，面向连接，不会丢失数据因此适合大数据量的交换。
- TCP 是面向字节流，UDP 面向报文，并且网络出现拥塞不会使得发送速率降低（因 此会出现丢包，对实时的应用比如 IP 电话和视频会议等）。
- TCP 只能是 1 对 1 的，而 UDP 支持 1 对 1,1 对多。
- TCP 的首部较大为 20 字节，而 UDP 只有 8 字节。
- TCP 是面向连接的可靠性传输，而 UDP 是不可靠的。
