---
title: Azure 中使用共享磁盘的 WSFC 上的群集 SAP ASCS/SCS 实例 | Microsoft Docs
description: 了解如何使用群集共享磁盘在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 480b8e7d5e728eca10901d753b825d1894711e70
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751647"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例

> ![Windows OS][Logo_Windows] Windows
>

Windows Server 故障转移群集是 Windows 中高可用性 SAP ASCS/SCS 安装和 DBMS 的基础。

故障转移群集是一组 1+n 个独立服务器（节点），这些服务器相互配合，共同提高应用程序和服务的可用性。 如果发生节点故障，Windows Server 故障转移群集会计算可能发生的故障数并保留正常运行的群集以提供应用程序和服务。 可从不同的仲裁模式中选择，以实现故障转移群集。

## <a name="prerequisites"></a>先决条件
在开始本文中的任务之前，请先查看以下文章：

* [适用于 SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Azure 中的 Windows Server 故障转移群集

使用 Azure 虚拟机的 Windows Server 故障转移群集需要执行额外的配置步骤。 生成群集时，需要为 SAP ASCS/SCS 实例设置多个 IP 地址和虚拟主机名。

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure 中的名称解析和群集虚拟主机名

Azure 云平台不提供配置虚拟 IP 地址（例如浮动 IP 地址）的选项。 需要一个替代解决方案来设置虚拟 IP，以便连接到云中的群集资源。 

Azure 负载均衡器服务提供适用于 Azure 的内部负载均衡器。 借助内部负载均衡器，客户端通过群集虚拟 IP 地址访问群集。 

在包含群集节点的资源组中部署内部负载均衡器。 然后，使用内部负载均衡器的探测端口配置所有必要的端口转发规则。 客户端可以通过虚拟主机名连接。 DNS 服务器解析群集 IP 地址，内部负载均衡器处理向活动群集节点的端口转发。

> [!IMPORTANT]
> 负载均衡方案中的 NIC 辅助 IP 配置不支持浮动 IP。 有关详细信息，请参阅 [Azure 负载均衡器限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果你需要为 VM 提供其他 IP 地址，请部署第二个 NIC。  

![图 1：Azure 中未使用共享磁盘的 Windows 故障转移群集配置][sap-ha-guide-figure-1001]

_Azure 中未使用共享磁盘的 Windows Server 故障转移群集配置_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>使用群集共享磁盘的 SAP ASCS/SCS HA
在 Windows 中，SAP ASCS/SCS 实例包含 SAP 中心服务、SAP 消息服务器、排队服务器进程和 SAP 全局主机文件。 SAP 全局主机文件存储整个 SAP 系统的中心文件。

SAP ASCS/SCS 实例具有以下组件：

* SAP 中心服务：
    * 两个进程（即消息和排队服务器）以及用于访问这两个进程的 \<ASCS/SCS virtual host name>。
    * 文件结构：S:\usr\sap\\&lt;SID&gt;\ASCS/SCS\<instance number\>


* SAP 全局主机文件：
  * 文件结构：S:\usr\sap\\&lt;SID&gt;\SYS\..
  * sapmnt 文件共享，可通过使用以下 UNC 路径实现对这些全局 S:\usr\sap\\&lt;SID&gt;\SYS\..文件的访问：

    \\\\<ASCS/SCS virtual host name\>\sapmnt\\&lt;SID&gt;\SYS\...


![图 2：SAP ASCS/SCS 实例的进程、文件结构和全局主机 sapmnt 文件共享][sap-ha-guide-figure-8001]

SAP ASCS/SCS 实例的进程、文件结构和全局主机 sapmnt 文件共享

在高可用性设置中，可群集化 SAP ASCS/SCS 实例。 我们使用群集共享磁盘（在示例中为驱动器 S）放置 SAP ASCS/SCS 文件和 SAP 全局主机文件。

![图 3：使用共享磁盘的 SAP ASCS/SCS HA 体系结构][sap-ha-guide-figure-8002]

使用共享磁盘的 SAP ASCS/SCS HA 体系结构


对于排队服务器复制 1 体系结构：
* 使用相同的 \<ASCS/SCS virtual host name> 访问 SAP 消息和排队服务器进程，并通过 sapmnt 文件共享访问 SAP 全局主机文件。
* 它们共用同一个群集共享磁盘驱动器 S。  

对于排队服务器复制 2 体系结构： 
* 使用相同的 \<ASCS/SCS virtual host name> 访问 SAP 消息服务器进程，并通过 sapmnt 文件共享访问 SAP 全局主机文件。
* 它们共用同一个群集共享磁盘驱动器 S。
* 单独的 \<ERS virtual host name> 访问排队服务器进程  

![图 4：使用共享磁盘的 SAP ASCS/SCS HA 体系结构][sap-ha-guide-figure-8003]

使用共享磁盘的 SAP ASCS/SCS HA 体系结构

#### <a name="shared-disk-and-enqueue-replication-server"></a>共享磁盘和排队复制服务器 

1. 排队服务器复制 1 体系结构支持共享磁盘，其中排队复制服务器 (ERS) 实例：   

   - 非群集化
   - 使用 `localhost` 名称
   - 部署在每个群集节点上的本地磁盘上

2. 排队服务器复制 2 体系结构也支持共享磁盘，其中排队复制服务器 2 (ERS2) 实例：  

   - 已群集化
   - 使用专用虚拟/网络主机名
   - 除 (A)SCS IP 地址外，还需要在 Azure 内部负载均衡器上配置 ERS 虚拟主机名的 IP 地址
   - 部署在每个群集节点上的本地磁盘上，因此不需要共享磁盘

   > [!TIP]
   > 可在下面找到有关排队复制服务器 1 和 2（ERS1 和 ERS2）的详细信息：  
   > [Microsoft 故障转移群集中的排队复制服务器](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [故障转移群集环境中的新排队复制器](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Azure 中用于 SAP 工作负荷的共享磁盘的选项

在 Azure 中，windows 故障转移群集中的共享磁盘有两个选项：

- [Azure 共享磁盘](../../disks-shared.md) - 允许同时将 Azure 托管磁盘附加到多个 VM 的功能。 
- 可使用第三方软件 [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) 创建模拟群集共享存储的镜像存储。 

为共享磁盘选择技术时，请记住以下注意事项：

**适用于 SAP 工作负荷的 Azure 共享磁盘**
- 允许同时将 Azure 托管磁盘附加到多个 VM，而无需额外的软件来维护和操作 
- 你将使用一个存储群集上的单个 Azure 共享磁盘来运行。 这会影响 SAP 解决方案的可靠性。
- 目前唯一受支持的部署是使用可用性集中的 Azure 共享高级磁盘。 区域部署中不支持 Azure 共享磁盘。     
- 请确保使用[高级 SSD 范围](../../disks-shared.md#disk-sizes)中指定的最小磁盘大小预配 Azure 高级磁盘，以便能够同时连接到所需数量的 VM（SAP ASCS Windows 故障转移群集通常为 2 个）。 
- SAP 工作负荷不支持 Azure 共享的超级磁盘，因为它不支持在可用性集中部署或区域部署。  
 
SIOS 
- SIOS 解决方案在两个磁盘之间提供实时同步数据复制。
- 使用 SIOS 解决方案，你可以对两个托管磁盘进行操作，而如果使用可用性集或可用性区域，托管磁盘将驻留在不同的存储群集上。 
- 支持在可用性区域中部署
- 需要安装并运行第三方软件，第三方软件将需要另外购买

### <a name="shared-disk-using-azure-shared-disk"></a>使用 Azure 共享磁盘的共享磁盘

Microsoft 提供的 [Azure 共享磁盘](../../disks-shared.md)可用于通过共享磁盘选项实现 SAP ASCS/SCS 高可用性。

#### <a name="prerequisites-and-limitations"></a>先决条件和限制

目前，可以使用 Azure 高级 SSD 盘作为 SAP ASCS/SCS 实例的 Azure 共享磁盘。 目前存在以下限制：

-  对于 SAP 工作负载，不支持将 [Azure 超级磁盘](../../disks-types.md#ultra-disk)作为 Azure 共享磁盘。 目前，不能在可用性集中使用 Azure 超级磁盘来放置 Azure VM
-  只有可用性集中的 VM 支持带有高级 SSD 盘的 [Azure 共享磁盘](../../disks-shared.md)。 它在可用性区域部署中不受支持。 
-  Azure 共享磁盘值 [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) 确定可以使用共享磁盘的群集节点数。 通常，对于 SAP ASCS/SCS 实例，你将在 Windows 故障转移群集中配置两个节点，因此 `maxShares` 的值必须设置为“2”。
-  所有 SAP ASCS/SCS 群集 VM 都必须部署在同一 [Azure 邻近位置组](../../windows/proximity-placement-groups.md)中。   
   尽管可以在没有 PPG 的情况下通过 Azure 共享磁盘在可用性集中部署 Windows 群集 VM，但 PPG 可以确保 Azure 共享磁盘与群集 VM 在物理上接近，从而降低 VM 与存储层之间的延迟。    

有关 Azure 共享磁盘限制的更多详细信息，请仔细阅读 Azure 共享磁盘文档的[限制](../../disks-shared.md#limitations)部分。

> [!IMPORTANT]
> 使用 Azure 共享磁盘部署 SAP ASCS/SCS Windows 故障转移群集时，请注意，部署将在一个存储群集中使用单个共享磁盘运行。 如果部署 Azure 共享磁盘的存储群集出现问题，SAP ASCS/SCS 实例将受到影响。    

> [!TIP]
> 规划 SAP 部署时，查看《[Azure 上的 SAP Netweaver 规划指南](./planning-guide.md)》和《[适用于 SAP 工作负荷的 Azure 存储指南](./planning-guide-storage.md)》，了解重要的注意事项。

### <a name="supported-os-versions"></a>支持的操作系统版本

支持 Windows Server 2016 和 2019（使用最新的数据中心映像）。

强烈建议使用 Windows Server 2019 Datacenter，原因如下：
- Windows 2019 故障转移群集服务可以感知 Azure
- 增加了 Azure 主机维护的集成和感知，并通过监视 Azure 计划事件改进了体验。
- 可以使用分布式网络名称（它是默认选项）。 因此，群集网络名称无需使用专用 IP 地址。 此外，无需在 Azure 内部负载均衡器上配置此 IP 地址。 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Azure 中使用 SIOS DataKeeper 的共享磁盘

共享磁盘的另一个选项是使用第三方软件 SIOS DataKeeper Cluster Edition 创建模拟群集共享存储的镜像存储。 SIOS 解决方案提供实时同步的数据复制。

为群集创建共享磁盘资源：

1. 将额外磁盘附加到 Windows 群集配置中的每个虚拟机。
2. 在两个虚拟机节点上都运行 SIOS DataKeeper Cluster Edition。
3. 配置 SIOS DataKeeper Cluster Edition，以便它将额外磁盘附加卷的内容从源虚拟机映射到目标虚拟机的额外磁盘附加卷。 SIOS DataKeeper 抽象化源和目标本地卷，并以单个共享磁盘形式呈现给 Windows Server 故障转移群集。

获取有关 [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/) 的详细信息。

![图 5：Azure 中使用 SIOS DataKeeper 的 Windows Server 故障转移群集配置][sap-ha-guide-figure-1002]

Azure 中使用 SIOS DataKeeper 的 Windows 故障转移群集配置

> [!NOTE]
> 对于某些 DBMS 产品（如 SQL Server），无需共享磁盘来实现高可用性。 SQL Server AlwaysOn 将 DBMS 数据和日志从一个群集节点的本地磁盘复制到另一个群集节点的本地磁盘。 在此情况下，Windows 群集配置不需要共享磁盘。
>
## <a name="optional-configurations"></a>可选配置

下图显示了 Azure VM 上运行 Microsoft Windows 故障转移群集以减少 VM 总数的多个 SAP 实例。

这可以是 SAP ASCS/SCS 群集上的本地 SAP 应用程序服务器，也可以是 Microsoft SQL Server Always On 节点上的 SAP ASCS/SCS 群集角色。

> [!IMPORTANT]
> 不支持在 SQL Server Always On 节点上安装本地 SAP 应用程序服务器。
>

SAP ASCS/SCS 和 Microsoft SQL Server 数据库都是单一故障点 (SPOF)。 为了在 Windows 环境中保护这些 SPOF，使用了 WSFC。

虽然 SAP ASCS/SCS 的资源消耗相当小，但建议将 SQL Server 或 SAP 应用程序服务器的内存配置减少 2 GB。

### <a name="sap-application-servers-on-wsfc-nodes-using-sios-datakeeper"></a>使用 SIOS DataKeeper 的 WSFC 节点上的 SAP 应用程序服务器

![图 6：Azure 中的 Windows Server 故障转移群集配置，其中包含 SIOS DataKeeper 和本地安装的 SAP 应用程序服务器][sap-ha-guide-figure-1003]

> [!NOTE]
> 由于 SAP 应用程序服务器安装在本地，因此无需按图中所示来设置任何同步。
>
### <a name="sap-ascsscs-on-sql-server-always-on-nodes-using-sios-datakeeper"></a>使用 SIOS DataKeeper 的 SQL Server Always On 节点上的 SAP ASCS/SCS

![图 7：使用 SIOS DataKeeper 的 SQL Server Always On 节点上的 SAP ASCS/SCS][sap-ha-guide-figure-1005]

[使用 Windows SOFS 的 WSFC 节点上的 SAP 应用程序服务器的可选配置][optional-fileshare]

[使用 NetApp Files SMB 的 WSFC 节点上的 SAP 应用程序服务器的可选配置][optional-smb]

[使用 Windows SOFS 的 SQL Server Always On 节点上的 SAP ASCS/SCS 的可选配置][optional-fileshare-sql]

[使用 NetApp Files SMB 的 SQL Server Always On 节点上的 SAP ASCS/SCS 的可选配置][optional-smb-sql]

## <a name="next-steps"></a>后续步骤

* [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构][sap-high-availability-infrastructure-wsfc-shared-disk]

* [对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘安装 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 多 SID 高可用性配置)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1003]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sios-as.png
[sap-ha-guide-figure-1005]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sql-ascs-sios.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sios.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[optional-smb]:high-availability-guide-windows-netapp-files-smb.md#5121771a-7618-4f36-ae14-ccf9ee5f2031 (使用 NetApp Files SMB 的 WSFC 节点上的 SAP 应用程序服务器的可选配置)
[optional-fileshare]:sap-high-availability-guide-wsfc-file-share.md#86cb3ee0-2091-4b74-be77-64c2e6424f50 (使用 Windows SOFS 的 WSFC 节点上的 SAP 应用程序服务器的可选配置)
[optional-smb-sql]:high-availability-guide-windows-netapp-files-smb.md#01541cf2-0a03-48e3-971e-e03575fa7b4f (使用 NetApp Files SMB 的 SQL Server Always On 节点上的 SAP ASCS/SCS 的可选配置)
[optional-fileshare-sql]:sap-high-availability-guide-wsfc-file-share.md#db335e0d-09b4-416b-b240-afa18505f503 (使用 Windows SOFS 的 SQL Server Always On 节点上的 SAP ASCS/SCS 的可选配置)