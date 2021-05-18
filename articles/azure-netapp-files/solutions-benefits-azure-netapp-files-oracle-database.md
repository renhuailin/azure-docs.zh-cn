---
title: 将 Azure NetApp 文件与 Oracle Database 配合使用的优点 | Microsoft Docs
description: 介绍技术并提供 Oracle Direct NFS (dNFS) 和传统 NFS 客户端之间的性能比较。 显示将 dNFS 与 Azure NetApp 文件配合使用的优点。
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932492"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>将 Azure NetApp 文件与 Oracle Database 配合使用的好处

利用 Oracle Direct NFS (dNFS)，可以实现比操作系统自己的 NFS 驱动程序更高的性能。 本文会对技术方面进行介绍并提供 dNFS 和传统 NFS 客户端（内核 NFS）之间的性能比较。 另外还会展示将 dNFS 与 Azure NetApp 文件配合使用的优点和便利性。  

## <a name="how-oracle-direct-nfs-works"></a>Oracle Direct NFS 的工作原理

以下摘要简略说明了 Oracle Direct NFS 的工作原理：

* Oracle Direct NFS 会绕过操作系统缓冲区缓存。 数据在用户空间只缓存一次，消除了内存副本的开销。  

* 传统 NFS 客户端使用单个网络流，如下所示：    

    ![使用单个网络流的传统 NFS 客户端](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS 通过对多个网络流中的网络流量进行负载均衡来进一步提高性能。 如下面的测试所示，Oracle Database 动态建立了 650 个非重复网络连接：  

    ![Oracle Direct NFS 提高性能](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Oracle 对 Direct NFS 常见问题的解答](http://www.orafaq.com/wiki/Direct_NFS)介绍了 Oracle dNFS 是经过优化的 NFS 客户端。 它提供对 NAS 存储设备上的 NFS 存储的快速、可缩放的访问（可通过 TCP/IP 进行访问）。 dNFS 就像 ASM 一样是内置在数据库内核中的，主要与 DAS 或 SAN 存储配合使用。 因此，指导原则是在实现 NAS 存储时使用 dNFS，在实现 SAN 存储时使用 ASM。

dNFS 是 Oracle 18c 中的默认选项。

dNFS 从 Oracle Database 11g 开始可用。 下图将 dNFS 与原生 NFS 进行了比较。 当你使用 dNFS 时，在 Azure 虚拟机上运行的 Oracle 数据库可以驱动比原生 NFS 客户端更多的 I/O。

![Oracle 和 Azure NetApp 文件对 dNFS 与原生 NFS 的比较](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

可以通过运行两个命令并重启数据库来启用或禁用 dNFS。

若要启用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

若要禁用此设置，请执行以下命令：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp 文件与 Oracle Direct NFS 相结合

可以通过 Azure NetApp 文件服务增强 Oracle dNFS 的性能。 此服务提供对应用程序性能的总体控制。 它可以满足要求极其苛刻的应用程序。 Oracle dNFS 与 Azure NetApp 文件的结合为工作负荷提供了极大的优势。

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)
- [Azure 上的 Oracle 应用程序和解决方案概述](../virtual-machines/workloads/oracle/oracle-overview.md)