---
categories: dev
date: 2015-11-12T09:19:00Z
tags: network internet
title: TCP auto-tuning
url: /2015/11/12/tcp-auto-tuning/
---

TCP auto-tuning is not new, but it's still not well-known. TCP performance has
two competing environmental issues tugging at it. If you queue too much, then
you have wild swings when congestion happens, causing your transmission rate
to swing between maximum and near-zero. If you don't have enough packets in
flight, then you are waiting in ACKs to be returned, and thus using only a
fraction of available bandwidth. Both of these have one input from you, the
size of the receive buffer (and lesser but still somewhat important, the
size of the send buffer).

TCP auto-tuning's insight was to let the TCP stack participate in setting the
size of TCP buffers. In the past 10 years, most major operating systems have
added TCP auto-tuning to their TCP network stacks. Linux as of 2.6, Windows
as of Vista, Mac OS X as of 10.5.

I think that most operating system disable auto-tuning if you manually set socket
buffer sizes. In BSD, this is done with setsockopt:

```c++
int bufferSize = 65536;
int optSize = sizeof(bufferSize);
setsockopt(sock, SOL_SOCKET, SO_SNDBUF, (char *)&bufferSize, optSize);
bufferSize = 65536;
setsockopt(sock, SOL_SOCKET, SO_RCVBUF, (char *)&bufferSize, optSize);
```

[Host Tuning](https://fasterdata.es.net/host-tuning/) - details for various operating systems on manual and automatic tuning.

[TCP Throughput Calculator](https://www.switch.ch/network/tools/tcp_throughput/)

[Enabling High Performance Data Transfers](http://www.psc.edu/index.php/networking/641-tcp-tune)

[Windows TCP Window Scaling Hitting plateau too early](http://serverfault.com/questions/608060/windows-tcp-window-scaling-hitting-plateau-too-early)

[TCP performance tuning - how to tune linux](http://www.acc.umu.se/~maswan/linux-netperf.txt)

[LINUX TCP AUTO-TUNING](https://littledaemons.wordpress.com/2009/03/25/linux-tcp-auto-tuning/)

[How-To disable Windows 7 TCP/IP auto-tuning](http://www.riccardoriva.info/blog/?p=1286)
