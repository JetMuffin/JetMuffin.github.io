title: InfiniBand技术介绍
date: 2016-06-07 09:51:44
tags: [hpc,网络,InfiniBand]
categories: 基础架构
---

> 随着服务器处理速度的不断加快，用户对提升网络传输速度的需求也越来越紧迫，传统意义上的输入 / 输出技术， 如 PCI、以太等，已经无法满足这种需求。如何更为有效的提高数据传输速度，增加有效带宽成为摆在人们面前必须解决的一个重大问题。 InfiniBand 标准就是在这种情况下应运而生的，它在很大程度上解决了传统输入 / 输出架构的传输瓶颈问题 , 运行速度可达到每端口 2.5Gb/s 或 10Gb/s 。 目前，这项技术已经广泛应用到高性能计算等领域，Linux 操作系统内核也对其提供了全面的支持，本文将逐一介绍 InfiniBand 技术架构，以及如何在 Red Hat Enterprise Linux 5.3 和 SLES11 系统中配置 InfiniBand 网络设备，包括网卡驱动的安装以及 IPoIB（IP over InfiniBand）接口配置。

<!-- more -->

### HPC集群
高性能计算（HPC）服务器集群在各个行业中正逐步流行开来，主要用于计算密集型任务。包括模拟物体表面上大气或流体流动的计算流体力学，财务风险分析，生物信息学，量子力学等方面计算。

### HPC集群中的网络
网络是HPC集群的核心，用于实现执行并行计算的计算节点之间的通信。

根据使用目的的不同，可以把HPC中的网络大体分为三种类型，在真正搭建HPC集群时使用其中的一种，两种或三种。

* **管理网络**：用于用户维护、控制和操作单个节点的网络
* **I/O网络**：用于对中央共享文件系统执行读写操作的网络
* **IPC网络**：用于为并行处理传递消息和数据的流程间通信网络

### InfiniBand vs 以太网
为了满足HPC集群中的网络需求，通常的解决方案有两种：以太网和InfiniBand。然而这两种网络的区别和使用范围还是很明显的。

传统数据中心常常使用以太网互联（这里的以太网多指千兆以太网），构成一张高速运转的数据网络。以太网采用树状结构，按三层交换机制排列，从核心开始扇形展开。
而高性能计算集群则常常使用InfiniBand。InfiniBand的拓扑结构相对更加扁平，如图1所示，它主要依赖于两个核心组件：主机通道适配器`HCA（Host Channel Adapter）`和InfiniBand交换机。其中HCA为主机设备提供一个接口用于支持所有InfiniBand定义的操作，而交换机则将一个端口接受到的InfiniBand保温转发到另一个端口，支持单播和多播。
![图1. InfiniBand拓扑结构](/uploads/images/2016/06/07/image001.jpg)

也是取决于InfiniBand的结构决定了它的带宽大、延迟低的优势，也正因为如此被广泛使用在高性能集群中。

近年来万兆以太网逐渐进入市场，它启用了CSMA/CD来处理数据包冲突，而采用全双工模式以降低延迟，同时它支持更大的带宽，然而万兆以太网在延迟上仍然比InfiniBand高，同时成本也相比InfiniBand高。但是在未来，万兆以太网可能会影响InfiniBand在高性能集群中的市场。

### InfiniBand在Linux中的使用
Red Hat产品中从Red Hat Enterprise Linux 5.3开始正式从内核中集成对InfiniBand网卡的支持，并开始讲InfiniBand所需的驱动以及库文件打包到发行版的CD里，所以推荐使用Red Hat 5.3之后的系统版本。而对于其他发行版的Linux，都在支持InfiniBand。

### 配置及错误总结

详见下一篇文章。

