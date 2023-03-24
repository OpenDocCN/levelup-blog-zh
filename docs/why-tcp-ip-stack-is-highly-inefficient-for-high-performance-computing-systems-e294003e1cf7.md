# 为什么 TCP/IP 协议栈对于高性能计算系统效率很低

> 原文：<https://levelup.gitconnected.com/why-tcp-ip-stack-is-highly-inefficient-for-high-performance-computing-systems-e294003e1cf7>

## 人工智能训练集群中 TCP/IP 的性能低效

![](img/0540ac21081d6ba45bc4c635ba8059e7.png)

阿纳斯塔塞·马拉戈斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去的十年中，数据量一直在快速增长。单台计算机已无法胜任这些大型数据计算任务。多台计算机被放在一起处理数据和进行计算。但是高计算能力只是在人工智能训练集群中实现低作业完成时间的一部分。这些系统之间的网络互连也应该是高性能的。否则，总的作业完成时间将总是受到网络的瓶颈限制。

在过去的二十年里，以太网已经从 100Mbps 发展到 400Gbps。随着硬件和网卡的发展，数据可以通过网络以非常高的速度快速传输。但要实现这一点，系统还应该能够足够快地处理数据包。否则，我们将永远无法利用完整的网络带宽。

# TCP 处理开销

[传输控制协议(TCP)](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 是当今网络世界中网络堆栈中被广泛接受的传输层协议。由于其可靠性、适应性和健壮性，TCP 在广泛的应用中继续占据主导地位。

一旦数据通过网络传输，传输层就要进行大量的处理。这种处理是实现 TCP 协议的可靠性和健壮性所必需的。其中很少包括流量控制、拥塞控制、校验和计算以及将数据传递给应用程序。

在过去几年中，千兆速度网络的发展主要在两个方面对 TCP 提出了挑战——性能和 CPU 要求。

让我们看看协议处理对 CPU 的要求。理论上，传输协议处理 1 位传输每秒需要 1 个 CPU 周期。随着网卡传输带宽的增加和更多数据通过网络传输，系统将消耗大量 CPU 周期进行协议处理。CPU 的大部分将被网络协议处理而不是应用计算所消耗。

让我们进入与协议处理相关的性能问题。影响性能的一个协议处理开销是操作系统。

协议处理需要在操作系统中做很多事情，比如接受中断、分配包缓冲区、释放包缓冲区、重启 IO 设备、唤醒进程以及管理定时器。这些会造成一部分处理开销，但不是主要的。

协议处理中影响性能的主要瓶颈是该过程中涉及的多个内存副本。

在 IP 协议组中，当传输数据时，在接收端会发生以下步骤，

*   [网络接口控制器](https://en.wikipedia.org/wiki/Network_interface_controller)将接收数据【1 读操作】并将中断内核
*   内核识别哪个应用程序数据属于哪个应用程序，并将唤醒那个应用程序
*   应用程序会将数据从套接字缓冲区复制到应用程序缓冲区[1 次读取和 1 次写入操作]
*   将计算校验和[1 次读取操作]

如上所述，从网络接收数据包需要四次内存访问操作。当今动态 ram 的典型周期时间为 250 ns 的 32 位存储器意味着存储器限制为 32 Mb/s，因此存储器访问必须变得更快，以便有效利用网络带宽速率。这些内存副本是 TCP 协议中最显著的性能低效之处。

多年来，开发了其他技术来避免 IP 网络堆栈中的处理开销和多个副本— InfiniBand 和远程直接内存访问。

# 无限带宽和 RDMA

[InfiniBand](https://en.wikipedia.org/wiki/InfiniBand#:~:text=InfiniBand%20%28IB%29%20is%20a%20computer,both%20among%20and%20within%20computers.&text=It%20is%20designed%20to%20be,a%20switched%20fabric%20network%20topology.) 是一种网络通信标准，用于高性能计算系统以实现高吞吐量和低延迟。它用作计算机之间的数据互连。它相当于以太网，一种物理层标准，以太网提供高达 400Gbps 的连接，而 Infiniband 仅提供高达 200Gbps 的连接。

Infiniband 支持 [RDMA](https://en.wikipedia.org/wiki/Remote_direct_memory_access#:~:text=In%20computing%2C%20remote%20direct%20memory,in%20massively%20parallel%20computer%20clusters.) ，这是一种零拷贝技术。它在通信过程中绕过内核，不涉及 CPU。这减少了大量的 CPU 开销，并且在性能上比 TCP 好得多。

RDMA 提供对另一个系统内存的访问，而不涉及任何一个系统的操作系统。使用 RDMA 通常需要实现 InfiniBand 的专用网络硬件。RDMA 技术使网络接口控制器能够在数据包到达时了解以下信息——该数据包属于哪个应用程序，以及该数据包应放在哪个应用程序缓冲存储器中。有了这些信息，数据将直接写入应用程序缓冲区，而不经过网络堆栈或涉及操作系统。这个过程需要 InfiniBand API 动词来执行 RDMA 操作，应用程序应该支持这个 API。

近年来， [RoCE](https://en.wikipedia.org/wiki/RDMA_over_Converged_Ethernet) —融合以太网上的 RDMA 得到了发展，在标准以太网网络接口控制器上提供 RDMA 功能，而无需支持 Infiniband 的专用硬件。

# 结论

由于操作系统的开销和内存中数据的多个副本，TCP 具有很高的处理开销。这使得它对于高性能和低延迟网络来说效率非常低。

RDMA 广泛应用于高速、低延迟网络，如人工智能训练集群。RDMA 有它的缺点，不适合各种工作负载，而且不如 TCP 灵活，也更复杂。

并非所有工作负载都需要高性能、低延迟，并且可能不会有繁重的数据传输，从而导致较高的处理开销。选择 TCP 还是 RDMA 取决于系统的工作负载和性能要求。

一定要看看我下面的其他相关文章。

[](https://medium.com/mlearning-ai/how-do-processes-communicate-in-parallel-computing-collective-communications-4419d90529c6) [## 并行计算中的进程是如何通信的？集体通信

### 集体通信导论

medium.com](https://medium.com/mlearning-ai/how-do-processes-communicate-in-parallel-computing-collective-communications-4419d90529c6) [](https://blog.devgenius.io/cuda-and-parallel-programming-on-gpu-dd698d2bd73d) [## CUDA 与 GPU 上的并行编程

### CUDA 和 GPU 上的并行编程如何加速计算能力

blog.devgenius.io](https://blog.devgenius.io/cuda-and-parallel-programming-on-gpu-dd698d2bd73d) [](https://medium.com/@getting.better.everyday/what-is-message-passing-interface-mpi-e5cf61d2bcde) [## 什么是消息传递接口(MPI)？

### 平均弹着点

) ?MPImedium.com](https://medium.com/@getting.better.everyday/what-is-message-passing-interface-mpi-e5cf61d2bcde) 

# 资源

1.  ，[，](https://ieeexplore.ieee.org/author/37289600400)，[王芳](https://ieeexplore.ieee.org/author/37085760778)，[梁明](https://ieeexplore.ieee.org/author/38487017100)，[谢雨来](https://ieeexplore.ieee.org/author/37534447500)，TCP 和 RDMA 在现代服务器平台上的性能深度分析(2010)[，](https://ieeexplore.ieee.org/document/6310890)
2.  大卫·d·克拉克，范·雅各布森，约翰·罗姆基，霍华德·萨尔温，TCP 处理开销分析(1989)，[https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&ar number = 29545](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=29545)
3.  雷纳托·约翰·雷西奥，万兆网络上的套接字与 RDMA 接口:对内存流量瓶颈的深入分析(2003 年)，[http://citeseerx.ist.psu.edu/viewdoc/download?doi = 10 . 1 . 1 . 77 . 3915&rep = re P1&type = pdf](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.77.3915&rep=rep1&type=pdf)