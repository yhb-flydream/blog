<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:19:41
 * @LastEditTime: 2023-11-29 17:24:17
 * @LastEditors: yaohebin
 * @Description: DNS 劫持&污染
-->

# DNS 劫持&污染

> **参考**
>
> [《通俗易懂：什么是 DNS 污染？什么是 DNS 劫持？区别又是什么？》(作者：)【来源：开心技术乐园】](https://www.hack520.com/330.html)
>
> [《DNS 污染与劫持》(作者：wqbin)【来源：博客园】](https://www.cnblogs.com/wqbin/p/12118458.html)

[TOC]

## DNS 劫持

通过劫持了 DNS 服务器，通过某些手段取得某域名的解析记录控制权，进而修改此域名的解析结果，导致对该域名的访问由原 IP 地址转入到修改后的指定 IP，其结果就是对特定的网址不能访问或访问的是假网址，从而实现窃取资料或者破坏原有正常服务的目的。**DNS 劫持通过篡改 DNS 服务器上的数据返回给用户一个错误的查询结果来实现的**。

## DNS 污染

又称为域名服务器缓存污染（DNS cache pollution）或者域名服务器快照侵害（DNS cache poisoning）。**DNS 污染是指一些刻意制造或无意中制造出来的域名服务器分组，把域名指往不正确的 IP 地址**。它是一种让一般用户由于得到虚假目标主机 IP 而不能与其通信的方法，是一种 DNS 缓存投毒攻击（DNS cache poisoning）。
