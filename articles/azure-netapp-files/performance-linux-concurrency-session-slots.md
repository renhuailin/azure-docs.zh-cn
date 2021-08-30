---
title: Azure NetApp 文件的 Linux 并发最佳做法 - 会话槽和槽表条目 | Microsoft Docs
description: 介绍有关 Azure NetApp 文件 NFS 协议的会话槽和槽表最佳做法。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: b-juche
ms.openlocfilehash: 522c9e590f1f63a12bd4f52f56eac0798ba78aa7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729228"
---
# <a name="linux-concurrency-best-practices-for-azure-netapp-files---session-slots-and-slot-table-entries"></a>Azure NetApp 文件的 Linux 并发最佳做法 - 会话槽和槽表条目

本文有助于你理解 Azure NetApp 文件 NFS 协议的会话槽和槽表条目的并发最佳做法。 

## <a name="nfsv3"></a>NFSv3

NFSv3 没有用于在客户端和服务器之间协商并发的机制。 客户端和服务器各自定义其限制，而无需咨询对方。  为获得最佳性能，应将客户端 `sunrpc` 槽表条目的最大数目与在服务器上支持的最大数目（不需要回推）对齐。  当客户端使服务器网络堆栈负担过重而无法处理工作负载时，服务器会通过减小连接的窗口大小进行响应，这不是理想的性能场景。

默认情况下，新式 Linux 内核将每连接 `sunrpc` 槽表条目大小 `sunrpc.max_tcp_slot_table_entries` 定义为支持 65,536 个未完成操作，如下表所示。 

| Azure NetApp 文件 NFSv3 服务器 <br> 每个连接的最大执行上下文数 | Linux 客户端 <br> 每个连接的默认最大 `sunrpc` 槽表条目数 |
|-|-|
| 128 | 65,536 |

这些槽表条目定义并发限制。 这么高的值是不必要的。  例如，使用名为 Littles Law 的排队理论，你会发现 I/O 速率是由并发（即未完成的 I/O）和延迟决定的。 因此，该算法证明，65,536 个槽比驱动极其苛刻的工作负载所需的槽数要高得多。

`Littles Law: (concurrency = operation rate × latency in seconds)`

低至 155 的并发级别足以使用 Oracle Direct NFS 实现每秒 155,000 个 Oracle DB NFS 操作，这是一种与 `nconnect` 装载选项概念类似的技术：

* 考虑到 0.5 毫秒的延迟，需要 55 个并发才能实现 110,000 IOPS。
* 考虑到 1 毫秒的延迟，需要 155 个并发才能实现 155,000 IOPS。

![Oracle DNFS 延迟曲线](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

有关详细信息，请参阅 [Azure NetApp 文件单卷上的 Oracle 数据库性能](performance-oracle-single-volumes.md)。

可优化的 `sunrpc.max_tcp_slot_table_entries` 参数是连接级优化参数。  最佳做法是将此值设置为每个连接 128 或更少，而不是超过 10,000 个槽环境范围。

### <a name="examples-of-slot-count-based-on-concurrency-recommendation"></a>基于并发建议进行槽计数的示例 

本部分中的示例演示了基于并发建议的槽计数。  

#### <a name="example-1--one-nfs-client-65536-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128-based-on-the-server-side-limit-of-128"></a>示例 1 – 对于基于服务器端限制 128 的最大并发数 128，一个 NFS 客户端，65,536 个 `sunrpc.max_tcp_slot_table_entries`，没有 `nconnect`

示例 1 基于默认 `sunrpc.max_tcp_slot_table_entry` 值为 65,536 的单个客户端工作负载，以及单个网络连接（即无 `nconnect`）。  在这种情况下，可实现并发 128。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP`) 
        * 从理论上讲，客户端在每个连接中只能向服务器发出不超过 65,536 个正在进行的请求。
        * 服务器将从此单个连接中接收不超过 128 个正在进行的请求。

#### <a name="example-2--one-nfs-client-128-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128"></a>示例 2 - 对于最大并发数 128，一个 NFS 客户端，128 个 `sunrpc.max_tcp_slot_table_entries`，没有 `nconnect`

示例 2 基于 `sunrpc.max_tcp_slot_table_entry` 值为 128 的单个客户端工作负载，但没有 `nconnect` 装载选项。  使用此设置，可以通过单个网络连接实现并发数 128。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * 客户端将在每个连接中向服务器发出不超过 128 个正在进行的请求。
        * 服务器将从此单个连接中接收不超过 128 个正在进行的请求。

#### <a name="example-3--one-nfs-client-100-sunrpcmax_tcp_slot_table_entries-and-nconnect8-for-a-maximum-concurrency-of-800"></a>示例 3 - 对于最大并发数 800，一个 NFS 客户端，100 个 `sunrpc.max_tcp_slot_table_entries`，`nconnect=8`

示例 3 基于单个客户端工作负载，但 `sunrpc.max_tcp_slot_table_entry` 值较低，为 100。  此时，使用 `nconnect=8` 装载选项将工作负载分散到 8 个连接。  使用此设置，并发 800 可跨 8 个连接实现分发。  此数量是实现 400,000 IOPS 所需的并发数。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP), Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)… Connection 8 (10.10.10.10:2049, 10.10.10.11:7321,TCP)`
    * 连接 1
        * 客户端将从该连接向服务器发出不超过 100 个正在进行的请求。
        * 对于此连接，服务器应接受来自客户端的不超过 128 个正在进行的请求。
    * 连接 2
        * 客户端将从该连接向服务器发出不超过 100 个正在进行的请求。
        * 对于此连接，服务器应接受来自客户端的不超过 128 个正在进行的请求。
    * `…`
    * `…`
    * 连接 8
        * 客户端将从该连接向服务器发出不超过 100 个正在进行的请求。
        * 对于此连接，服务器应接受来自客户端的不超过 128 个正在进行的请求。

#### <a name="example-4--250-nfs-clients-8-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-2000"></a>示例 4 - 对于最大并发数 2000，250 个 NFS 客户端，8 `sunrpc.max_tcp_slot_table_entries`，没有 `nconnect`

示例 4 对 250 台计算机计数 EDA 环境使用减少的每个客户端 `sunrpc.max_tcp_slot_table_entry` 值 8。 在此场景中，2000 并发数达到环境范围，该值足以驱动 4,000 MiB/s 的后端 EDA 工作负载。

* `NFS_Server=10.10.10.10, NFS_Client1=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * 客户端将在每个连接中向服务器发出不超过 8 个正在进行的请求。
        * 服务器将从此单个连接中接收不超过 128 个正在进行的请求。
* `NFS_Server=10.10.10.10, NFS_Client2=10.10.10.12`
    * `Connection (10.10.10.10:2049, 10.10.10.12:7820,TCP) `
        * 客户端将在每个连接中向服务器发出不超过 8 个正在进行的请求。
        * 服务器将从此单个连接中接收不超过 128 个正在进行的请求。
* `…`
* `…`
* `NFS_Server=10.10.10.10, NFS_Client250=10.10.11.13`
    * `Connection (10.10.10.10:2049, 10.10.11.13:4320,TCP) `
        * 客户端将在每个连接中向服务器发出不超过 8 个正在进行的请求。
        * 服务器将从此单个连接中接收不超过 128 个正在进行的请求。

使用 NFSv3 时，应将存储终结点槽总计数保持在 10,000 或更低。 当应用程序跨多个网络连接横向扩展时（通常是 `nconnect` 和 HPC，特别是 EDA），最好将 `sunrpc.max_tcp_slot_table_entries` 的每个连接值设置为小于 128。  

### <a name="how-to-calculate-the-best-sunrpcmax_tcp_slot_table_entries"></a>如何计算最佳 `sunrpc.max_tcp_slot_table_entries` 

使用 Littles Law，可以计算所需的槽表条目总数。 通常情况下，请考虑以下因素：  

* 横向扩展工作负载在本质上通常是较大的有序工作负载。
* 数据库工作负载（尤其是 OLTP）本质上通常是随机的。 

下表显示了具有任意延迟的并发的示例研究：

|     I/O 大小    |     延迟    |     I/O 或吞吐量    |     并发    |
|-|-|-|-|
|     8 KiB    |     0.5 毫秒    |     110,000 IOPS \| 859 MiB/s    |     55    |
|     8 KiB    |     2 毫秒    |     400,000 IOPS \| 3,125 MiB/s    |     800    |
|     256 KiB    |     2 毫秒    |     16,000 IOPS \| 4,000 MiB/s    |     32    |
|     256 KiB    |     4 毫秒    |     32,000 IOPS \| 8,000 MiB/s    |     128    |

### <a name="how-to-calculate-concurrency-settings-by-connection-count"></a>如何按连接计数计算并发设置

例如，如果工作负载是一个 EDA 场，并且 1,250 个客户端都将工作负载驱动到同一存储终结点（存储终结点是存储 IP 地址），则计算所需的 I/O 速率，并在该场之间划分并发数。

假设工作负载为 4,000 MiB/s，平均操作大小为 256-KiB，平均延迟为 10 毫秒。 要计算并发数，请使用以下公式：

`(concurrency = operation rate × latency in seconds)`

计算结果为 160 并发数： 
 
`(160 = 16,000 × 0.010)`

考虑到需要 1,250 个客户端，可以安全地将 `sunrpc.max_tcp_slot_table_entries` 设置为每个客户端 2 个，以达到 4,000 MiB/s。  但是，你可能会决定通过将每个客户端的数量设置为 4 或甚至 8 来增加额外的空余空间，保持在建议的 10,000 个槽上限以下。 

### <a name="how-to-set-sunrpcmax_tcp_slot_table_entries-on-the-client"></a>如何在客户端上设置 `sunrpc.max_tcp_slot_table_entries`

1.  向 `/etc/sysctl.conf` 配置文件添加 `sunrpc.max_tcp_slot_table_entries=<n>`。   
    在优化期间，如果发现小于 128 的值是最佳值，请将 128 替换为相应的数字。
2.  运行以下命令：   
    `$ sysctl -p`
3.  装载（或重新装载）所有 NFS 文件系统，因为可优化参数仅适用于设置了可优化参数后进行的装载。

## <a name="nfsv41"></a>NFSv4.1 

在 NFSv4.1 中，会话定义客户端和服务器之间的关系。 无论装载的 NFS 文件系统位于一个连接上还是多个连接上（就像 `nconnect` 的情况），会话规则都适用。 设置会话时，客户端和服务器协商会话的最大请求数，并确定两个支持值的较低值。 Azure NetApp 文件 180 个未完成的请求，Linux 客户端则默认为 64 个。 下表说明了会话限制： 

| Azure NetApp 文件 NFSv4.1 服务器 <br>  每个会话的最大命令数 | Linux 客户端 <br>  每个会话的默认最大命令数    | 会话的协商最大命令数 |
|-|-|-|
|     180    |     64    |     64    |

尽管 Linux 客户端默认每个会话的最大请求数为 64，但 `max_session_slots` 的值是可调整的。  若要使更改生效，需要重新启动。  使用 `systool -v -m nfs` 命令查看客户端当前使用的最大值。  若要使命令正常运作，必须至少安装一个 NFSv4.1 装载：

```
$ systool -v -m nfs
{
Module = "nfs"
…
  Parameters:
…
    max_session_slots   = "64"   
…
}
```

若要优化 `max_session_slots`，请基于 `/etc/modprobe.d` 创建配置文件。  请确保文件行中没有“引号”。 否则，选项将不会生效。

`$ echo “options nfs max_session_slots=180” > /etc/modprobe.d/nfsclient.conf`
`$ reboot`

Azure NetApp 文件将每个会话限制为最多 180 个命令。 因此，请考虑当前可配置的最大值 180。  除非会话被划分到多个连接之间，否则客户端将无法实现大于 128 的并发，因为 Azure NetApp 文件将每个连接限制为最多 128 个 NFS 命令。  若要获取多个连接，建议使用 `nconnect` 装载选项，并且需要两个或多个值。

### <a name="examples-of-expected-concurrency-maximums"></a>预期最大并发数示例

本部分中的示例展示了预期最大并发数。  

#### <a name="example-1--64-max_session_slots-and-no-nconnect"></a>示例 1 – `max_session_slots` 为 64 且没有 `nconnect`

示例 1 基于 `max_session_slots` 的默认设置 64，没有 `nconnect`。 使用此设置，可以通过单个网络连接实现并发数 64。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * 客户端将在会话中向服务器发出不超过 64 个正在进行的请求。
        * 服务器将在会话中从客户端那里接收不超过 64 个正在进行的请求。 （64 为协商值。）

#### <a name="example-2--64-max_session_slots-and-nconnect2"></a>示例 2 – `max_session_slots` 为 64 且 `nconnect=2` 

示例 2 基于 `session_slots` 最大值 64，但添加了装载选项 `nconnect=2`。  并发数 64 是可以实现的，但在两个连接之间划分。  尽管在此场景中，多个连接不会带来更大的并发数，但每个连接的队列深度降低会对延迟产生积极影响。

当 `max_session_slots` 仍为 64 但 `nconnect=2` 时，请注意，请求的最大数量在连接之间划分。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * 连接 1
        * 客户端将从该连接向服务器发出不超过 32 个正在进行的请求。
        * 对于此连接，服务器应接受来自客户端的不超过 32 个正在进行的请求。
    * 连接 2
        * 客户端将从该连接向服务器发出不超过 32 个正在进行的请求。
        * 对于此连接，服务器应接受来自客户端的不超过 32 个正在进行的请求。

#### <a name="example-3--180-max_session_slots-and-no-nconnect"></a>示例 3 – `max_session_slots` 为 180 且没有 `nconnect`

示例 3 删除 `nconnect` 装载选项，将 `max_session_slots` 值设置为 180，与服务器的最大 NFSv4.1 会话并发数匹配。  在此场景中，如果只有一个连接，并且 Azure NetApp 文件每个 NFS 连接的最大未完成操作数为 128，则会话限制为 128 个运行中的操作。

虽然 `max_session_slots` 已设置为 180，但单个网络连接限制为最多 128 个请求，如下所示： 

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * 客户端将在会话中向服务器发出不超过 180 个正在进行的请求。
        * 服务器将在会话中从客户端那里接收不超过 180 个正在进行的请求。
        * 服务器将在单个连接中接收不超过 128 个正在进行的请求。

#### <a name="example-4--180-max_session_slots-and-nconnect2-"></a>示例 4 – `max_session_slots` 为180 且 `nconnect=2 ` 

示例 4 添加了 `nconnect=2` 装载选项，并重复使用 `max_session_slots` 值 180。 由于整个工作负载划分到两个连接，因此可实现 180 个未完成的操作。

如果两个连接均正常运行，则会话支持 180 个未完成请求的完整分配。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * 连接 1
        * 客户端应在连接 1 中维护向服务器发送的不超过 90 个正在进行的请求。
        * 对于此会话中的连接，服务器应维护来自客户端的不超过 90 个正在进行的请求。
    * 连接 2
        * 客户端应在连接 1 中维护向服务器发送的不超过 90 个正在进行的请求。
        * 对于此会话中的连接，服务器应维护来自客户端的不超过 90 个正在进行的请求。

> [!NOTE]
> 对于最大并发数，请将 `max_session_slots` 设置为 180，这是当前 Azure NetApp 文件支持的最大会话级别的并发数。

### <a name="how-to-check-for-the-maximum-requests-outstanding-for-the-session"></a>如何检查会话未完成的最大请求数 

若要查看客户端和服务器支持的 `session_slot` 大小，请捕获数据包跟踪中的装载命令。  查找 `CREATE_SESSION` 调用和 `CREATE_SESSION` 回复，如以下示例所示。  调用源自客户端，回复源自服务器。

使用以下 `tcpdump` 命令捕获装载命令：

`$ tcpdump -i eth0 -s 900 -w /tmp/write.trc port 2049`

使用 Wireshark 时，相关数据包如下所示：

![屏幕截图显示相关数据包。](../media/azure-netapp-files/performance-packets-interest.png)  

在这两个数据包中，查看跟踪文件中间部分的 `max_reqs` 字段。

* 网络文件系统
    * Operations
        * `Opcode`
            * `csa_fore_channel_attrs`
            * `max reqs`

数据包 12（客户端最大请求数）显示客户端的 `max_session_slots` 值为 64。  下一节中，请注意，服务器支持的会话并发数为 180。  会话最终协商确定两个提供的值中的较小值。

![屏幕截图显示数据包 12 的最大会话槽数。](../media/azure-netapp-files/performance-max-session-packet-12.png)  

以下示例显示数据包 14（服务器最大请求数）：

![屏幕截图显示数据包 14 的最大会话槽数。](../media/azure-netapp-files/performance-max-session-packet-14.png) 

## <a name="next-steps"></a>后续步骤  

* [适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法](performance-linux-direct-io.md)
* [适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法](performance-linux-filesystem-cache.md)
* [适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法](performance-linux-mount-options.md)
* [Linux NFS 文件预读最佳做法](performance-linux-nfs-read-ahead.md)
* [Azure 虚拟机 SKU 最佳做法](performance-virtual-machine-sku.md) 
* [Linux 性能基准](performance-benchmarks-linux.md) 
