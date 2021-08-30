---
title: 适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法 | Microsoft Docs
description: 介绍装载选项以及在 Azure NetApp 文件中使用这些选项的最佳做法。
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
ms.date: 06/01/2021
ms.author: b-juche
ms.openlocfilehash: 445920e1846e668f0baa3567111f169fd6dd7508
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232513"
---
# <a name="linux-nfs-mount-options-best-practices-for-azure-netapp-files"></a>适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法

本文将帮助你了解装载选项以及在 Azure NetApp 文件中使用这些选项的最佳做法。

## `Nconnect` 

使用 `nconnect` 装载选项可以指定 NFS 客户端和 NFS 终结点之间应建立的连接数（网络流），最多不超过 16 个。 通常，NFS 客户端在其自身和 NFS 终结点之间使用单一连接。  通过增加网络流的数量，I/O 和吞吐量的上限会大幅增加。 测试发现 `nconnect=8` 性能最高。  

为生产准备多节点 SAS 网格环境时，你可能会注意到运行时可重复减少 30%，从 8 小时减少到 5.5 小时： 

| 装载选项 | 作业运行时间 |
|-|-|
| 无 `nconnect` | 8 小时 |
| `nconnect=8`  | 5.5 小时 | 

这两组测试使用同一 E32-8_v4 虚拟机和 RHEL8.3，并将预读设置为 15 MiB。

使用 `nconnect` 时，请记住以下规则：

* Azure NetApp 文件在所有主要 Linux 发行版上均支持 `nconnect`，但仅限较新版本：

    | Linux 版本 | NFSv3（最低版本） | NFSv4.1（最低版本） |
    |-|-|-|
    | Redhat Enterprise Linux | RHEL8.3 | RHEL8.3 |
    | SUSE | SLES12SP4 或 SLES15SP1 | SLES15SP2 |
    | Ubuntu | Ubuntu18.04 |          |

    > [!NOTE]
    > SLES15SP2 是最低 SUSE 版本，在该版本中，`nconnect` 由 NFSv4.1 的 Azure NetApp 文件支持。  指定的所有其他版本均为引入 `nconnect` 功能的第一个版本。

* 来自单个终结点的所有装载都将继承装载的第一个导出的 `nconnect` 设置，如以下场景所示： 

    场景 1：`nconnect` 由第一次装载使用。 因此，针对相同终结点的所有装载均使用 `nconnect=8`。

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.10.10.10:/volume2 /mnt/volume2`
    * `mount 10.10.10.10:/volume3 /mnt/volume3`

    场景 2：`nconnect` 不由第一次装载使用。 因此，没有针对相同终结点的装载使用 `nconnect`，即使由此可以指定 `nconnect`。

    * `mount 10.10.10.10:/volume1 /mnt/volume1`
    * `mount 10.10.10.10:/volume2 /mnt/volume2 -o nconnect=8`
    * `mount 10.10.10.10:/volume3 /mnt/volume3 -o nconnect=8`

    场景 3：`nconnect` 设置不会跨不同的存储终结点进行传播。  `nconnect` 由来自 `10.10.10.10` 的装载使用，而不由来自 `10.12.12.12` 的装载使用。

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.12.12.12:/volume2 /mnt/volume2`

* `nconnect` 可用于从任何给定客户端增加存储并发。 

有关详细信息，请参阅[适用于 Azure NetApp 文件的 Linux 并发最佳做法](performance-linux-concurrency-session-slots.md)。

## <a name="rsize-and-wsize"></a>`Rsize` 和 `Wsize`
 
`rsize` 和 `wsize` 标志设置 NFS 操作的最大传输大小。  如果未在装载上指定 `rsize` 或 `wsize`，则客户端和服务器将协商这两者支持的最大大小。   目前，Azure NetApp 文件和新式 Linux 发行版都支持高达 1,048,576 字节 (1 MiB) 的读取和写入大小。   但是，为了获得最佳的总体吞吐量和延迟，Azure NetApp 文件建议将 `rsize` 和 `wsize` 设置为不大于 262,144 字节 (256 K)。 你可能会发现，使用大于 256 KiB 的 `rsize` 和 `wsize` 时，延迟增加，吞吐量降低。 

例如，[使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md#mount-the-azure-netapp-files-volumes)显示 256-KiB `rsize` 和 `wsize` 最大值，如下所示：

```
sudo vi /etc/fstab
# Add the following entries
10.23.1.5:/HN1-data-mnt00001 /hana/data/HN1/mnt00001  nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-data-mnt00002 /hana/data/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-log-mnt00001 /hana/log/HN1/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
```
 
又如，SAS Viya 建议使用 256-KiB 读取和写入大小，而 [SAS 网格](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/606973/highlight/true#M17740)将 `r/wsize` 限制为 64 KiB，同时通过增加的 NFS 装载的预读扩充读取性能。 有关详细信息，请参阅 [Azure NetApp 文件的 NFS 预读最佳做法](performance-linux-nfs-read-ahead.md)。

以下注意事项适用于 `rsize` 和 `wsize` 的使用：

* 随机 I/O 操作大小通常小于 `rsize` 和 `wsize` 装载选项。 因此，实际上它们不会受到约束。
* 使用文件系统缓存时，顺序 I/O 将以 `rsize` 和 `wsize` 装载选项所预测的大小发生，除非文件大小小于 `rsize` 和 `wsize`。
* 绕过文件系统缓存的操作，尽管仍然受到 `rsize` 和 `wsize` 装载选项的限制，但不一定会生成 `rsize` 或 `wsize` 指定的最大值。  使用具有 `directio` 选项的工作负载生成器时，这种考虑尤其重要。

作为 Azure NetApp 文件的最佳做法，为了获得最佳的总体吞吐量和延迟，请将 `rsize` 和 `wsize` 设置为不大于 262,144 字节。

## <a name="close-to-open-consistency-and-cache-attribute-timers"></a>Close-to-open 一致性和缓存属性计时器   

NFS 使用松散的一致性模型。 一致性是松散的，因为应用程序不必每次都去共享存储并获取数据来使用它，这种情况将对应用程序性能产生巨大影响。  可通过两种机制来管理此过程：缓存属性计时器和 close-to-open 一致性。

如果客户端拥有数据的完全所有权，也就是说，它不是在多个节点或系统之间共享的，那么就保证了一致性。 在这种情况下，可通过禁用 close-to-open (`cto`) 一致性（`nocto` 作为装载选项）并启用属性缓存管理的超时（`actimeo=600` 作为装载选项将计时器更改为 10m 而不是默认值 `acregmin=3,acregmax=30,acdirmin=30,acdirmax=60`），来减少对存储的 `getattr` 访问操作，并加快应用程序的速度。 在一些测试中，`nocto` 减少了大约 65-70% 的 `getattr` 访问调用，而调整 `actimeo` 又将这些调用减少了 20-25%。

### <a name="how-attribute-cache-timers-work"></a>属性缓存计时器的工作方式  

属性 `acregmin`、`acregmax`、`acdirmin` 和 `acdirmax` 控制缓存的一致性。 前两个属性控制文件属性受信任的时长。 后两个属性控制目录文件本身的属性受信任的时长（目录大小、目录所有权、目录权限）。  `min` 和 `max` 属性分别定义了目录属性、文件属性和文件缓存内容被视为可信的最短和最长持续时间。 在 `min` 和 `max` 之间，使用一种算法来定义信任缓存项的持续时间。

例如，分别考虑默认 `acregmin` 和 `acregmax` 值 3 秒和 30 秒。  例如，对目录中的文件重复计算属性。  3 秒钟后，将查询 NFS 服务的新鲜度。  如果属性被认为是有效的，那么客户端将信任时间加倍为 6 秒、12 秒、24 秒，然后将最大值设置为 30 秒。  从这时起，直到缓存的属性被视为过期（此时循环开始），可信度定义为 30 秒，即 `acregmax` 指定的值。

在其他情况下，可以从一组类似的装载选项中获益，即使客户端没有完全的所有权，例如，客户端将数据用作只读数据，并且通过另一个路径管理数据更新。  对于使用诸如 EDA、Web 托管和电影渲染等客户端网格的应用程序，以及具有相对静态的数据集（EDA 工具或库、Web 内容、纹理数据）的应用程序，典型的行为是数据集主要缓存在客户端上。 读取操作非常少，且无写入操作。 有大量 `getattr`/access 调用返回到存储。  这些数据集通常通过另一个装载文件系统的客户端进行更新，并定期推送内容更新。

在这些情况下，获取新内容时存在已知的滞后时间，应用程序仍然可以处理可能过期的数据。  在这些情况下，可使用 `nocto` 和 `actimeo` 来控制管理过期数据的时间段。  例如，在 EDA 工具和库中，`actimeo=600` 运行良好，因为这些数据通常很少更新。  对于小型 Web 托管，如果客户端在编辑站点时需要及时查看其数据更新，`actimeo=10` 可能是可以接受的。 对于将内容推送到多个文件系统的大型网站，`actimeo=60` 可能是可以接受的。

在这些情况下，使用这些装载选项可显著减少工作负载。 （例如，最近的 EDA 体验将工具卷的 IOP 从大于 150 K 降低到约 6 K。）应用程序可以运行得更快，因为它们可以信任内存中的数据。 （对于快速网络上的 `getattr`/access，内存访问时间为纳秒而不是数百微秒。）

### <a name="close-to-open-consistency"></a>Close-to-open 一致性 

Close-to-open 一致性（`cto` 装载选项）确保无论缓存的状态如何，打开时文件的最新数据总是呈现给应用程序。  

* 目录已爬网（例如 `ls`、`ls -l`）时，将发出一组 PRC 调用。  
    NFS 服务器共享其文件系统的视图。 只要访问给定 NFS 导出的所有 NFS 客户端都使用 `cto`，所有客户端都将看到其中相同的文件和目录列表。  目录中文件属性的新鲜度由[属性缓存计时器](#how-attribute-cache-timers-work)控制。  换而言之，只要使用 `cto`，一旦文件被创建并且文件进入存储，远程客户端就会看到文件。
* 打开文件时，从 NFS 服务器的角度来看，文件的内容保证是新鲜的。  
    如果在计算机 2 上打开一个文件时，存在一个争用条件，其中内容尚未从计算机 1 完成刷新，则计算机 2 将只接收打开时服务器上存在的数据。 在这种情况下，在到达 `acreg` 计时器之前，计算机 2 将不会从文件中检索更多数据，并且计算机 2 将再次从服务器检查其缓存一致性。  当文件仍在从计算机 1 写入时，使用计算机 2 的尾随 `-f` 可以观察到这种情况。

### <a name="no-close-to-open-consistency"></a>无 close-to-open 一致性  

当不使用 close-to-open 一致性 (`nocto`) 时，客户端将信任文件和目录的当前视图的新鲜度，直到违反缓存属性计时器为止。  

* 目录已爬网（例如 `ls`、`ls -l`）时，将发出一组 PRC 调用。  
    仅当违反 `acdir` 缓存计时器值时，客户端才会对服务器发出一个当前文件列表的调用。  在这种情况下，将不会显示最近创建的文件和目录，而仍显示最近删除的文件和目录。  

* 打开文件时，只要文件仍在缓存中，就会返回其缓存内容（如果有），而不验证与 NFS 服务器的一致性。

## <a name="next-steps"></a>后续步骤  

* [适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法](performance-linux-direct-io.md)
* [适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法](performance-linux-filesystem-cache.md)
* [适用于 Azure NetApp 文件的 Linux 并发最佳做法](performance-linux-concurrency-session-slots.md)
* [Linux NFS 文件预读最佳做法](performance-linux-nfs-read-ahead.md)
* [Azure 虚拟机 SKU 最佳做法](performance-virtual-machine-sku.md) 
* [Linux 性能基准](performance-benchmarks-linux.md) 
