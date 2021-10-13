---
title: 在 RHEL 上使用 ANF 实现 SAP HANA 纵向扩展的高可用性 | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 上使用 ANF 建立 SAP HANA 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/08/2021
ms.author: radeltch
ms.openlocfilehash: bd1841352111af49a9c40df2bed11325686e4017
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716195"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>在 Red Hat Enterprise Linux 上使用 Azure NetApp 文件实现 SAP HANA 纵向扩展的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文介绍通过 NFS 装载 HANA 文件系统时，如何在纵向扩展部署中使用 Azure NetApp 文件 (ANF) 配置 SAP HANA 系统复制。 在示例配置和安装命令中，使用实例编号 03 和 HANA 系统 ID HN1。 SAP HANA 复制由一个主节点和至少一个辅助节点组成。

如果本文档中的步骤标有以下前缀，则含义如下：

- **[A]** ：该步骤适用于所有节点
- **[1]** ：该步骤仅适用于 node1
- **[2]** ：该步骤仅适用于 node2
 
请先阅读以下 SAP 说明和文档：

- SAP 说明 [1928533](https://launchpad.support.sap.com/#/notes/1928533)，其中包含：
    - SAP 软件部署支持的 Azure VM 大小的列表。
    - Azure VM 大小的重要容量信息。
    - 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
    - Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
- SAP 说明 [2015553](https://launchpad.support.sap.com/#/notes/2015553) 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
- SAP 说明 [405827](https://launchpad.support.sap.com/#/notes/405827) 列出了适用于 HANA 环境的建议文件系统。
- SAP 说明 [2002167](https://launchpad.support.sap.com/#/notes/2002167) 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置。
- SAP 说明 [2009879](https://launchpad.support.sap.com/#/notes/2009879) 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 指南。
- SAP 说明 [2178632](https://launchpad.support.sap.com/#/notes/2178632) 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
- SAP 说明 [2191498](https://launchpad.support.sap.com/#/notes/2191498) 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
- SAP 说明 [2243692](https://launchpad.support.sap.com/#/notes/2243692) 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
- SAP 说明 [1999351](https://launchpad.support.sap.com/#/notes/1999351) 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
- [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
- [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
- [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
- [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
- [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)（Pacemaker 群集中的 SAP HANA 系统复制）。
- 通用 RHEL 文档
    - [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
    - [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（High Availability Add-On 管理）。
    - [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)。
    - [Configure SAP HANA System Replication in Scale-Up in a Pacemaker cluster when the HANA filesystems are on NFS shares](https://access.redhat.com/solutions/5156571)（当 HANA 文件系统位于 NFS 共享上时，在 Pacemaker 群集中配置 SAP HANA 纵向扩展系统复制）
- Azure 特定的 RHEL 文档：
    - [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）。
    - [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本]高可用性群集）。
    - [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)（在 Red Hat Enterprise Linux 上安装要在 Microsoft Azure 中使用的 SAP HANA）。
    - [Configure SAP HANA scale-up system replication up Pacemaker cluster when the HANA file systems are on NFS shares](https://access.redhat.com/solutions/5156571)（当 HANA 文件系统位于 NFS 共享上时，在 Pacemaker 群集中配置 SAP HANA 纵向扩展系统复制）
- [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序](https://www.netapp.com/us/media/tr-4746.pdf)
- [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)

## <a name="overview"></a>概述

传统上，在纵向扩展环境中，SAP HANA 的所有文件系统都从本地存储进行装载。 [在 RHEL 上设置 SAP HANA 系统复制](./sap-hana-high-availability-rhel.md)指南中发布了如何在 Red Hat Enterprise Linux 上设置 SAP HANA 系统复制的高可用性的说明

若要在 [Azure NetApp 文件](../../../azure-netapp-files/index.yml) NFS 共享上实现纵向扩展系统的 SAP HANA 高可用性，我们需要在群集中进行一些其他资源配置，以便在一个节点失去对 ANF 上的 NFS 共享的访问权限时恢复 HANA 资源。  群集管理 NFS 装载，这使其可以监视资源的运行状况。 文件系统装载和 SAP HANA 资源之间的依赖关系会强制执行。  

![ANF 上的 SAP HANA HA 纵向扩展](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

在每个节点上使用 Azure NetApp 文件将 SAP HANA 文件系统装载到 NFS 共享。 文件系统 /hana/data、/hana/log 和 /hana/shared 对于每个节点都是唯一的。 

已在 node1 (hanadb1) 上装载

- /hana/data 上的 10.32.2.4:/hanadb1-data-mnt00001
- /hana/log 上的 10.32.2.4:/hanadb1-log-mnt00001
- /hana/shared 上的 10.32.2.4:/hanadb1-shared-mnt00001

已在 node2 (hanadb2) 上装载

- /hana/data 上的 10.32.2.4:/hanadb2-data-mnt00001
- /hana/log 上的 10.32.2.4:/hanadb2-log-mnt00001
- /hana/shared 上的 10.32.2.4:/hanadb2-shared-mnt00001

> [!NOTE]
> 两个节点之间不共享文件系统 /hana/shared、/hana/data 和 /hana/log。 每个群集节点都有自己的单独文件系统。   

SAP HANA 系统复制配置使用专用的虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示负载均衡器的配置：

- 前端配置：hn1-db 的 IP 地址 10.32.0.10
- 后端配置：连接到应当作为 HANA 系统复制的一部分的所有虚拟机的主网络接口
- 探测端口：端口 62503
- 负载均衡规则：30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP（如果使用基本 Azure 负载均衡器）  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>设置 Azure NetApp 文件基础结构

在继续设置 Azure NetApp 文件基础结构之前，请先通过 [Azure NetApp 文件文档](../../../azure-netapp-files/index.yml)熟悉该基础结构。

Azure NetApp 文件在多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 查看所选 Azure 区域是否提供 Azure NetApp 文件。

有关 Azure NetApp 文件在各 Azure 区域的可用性的信息，请参阅[按 Azure 区域划分的 Azure NetApp 文件可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和 VM（将装载 Azure NetApp 文件资源）必须部署在同一 Azure 虚拟网络或对等 Azure 虚拟网络中。

1. 按照[创建 NetApp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的说明，在所选的 Azure 区域中创建一个 NetApp 帐户。

2.  按照[设置 Azure NetApp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明，设置 Azure NetApp 文件容量池。

    本文中介绍的 HANA 体系结构使用超高性能服务级别的单个 Azure NetApp 文件容量池。 对于 Azure 上的 HANA 工作负载，建议使用 Azure NetApp 文件超高性能或高级[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

3.  按照[将子网委派给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明所述，将子网委派给 Azure NetApp 文件。

4.  按照[为 Azure NetApp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明部署 Azure NetApp 文件卷。

    部署卷时，请务必选择 NFSv4.1 版本。 将卷部署在指定的 Azure NetApp 文件子网中。 将自动分配 Azure NetApp 卷的 IP 地址。

    请记住，Azure NetApp 文件资源和 Azure VM 必须位于同一 Azure 虚拟网络或对等 Azure 虚拟网络中。 例如，hanadb1-data-mnt00001、hanadb1-log-mnt00001 等是卷名称，nfs://10.32.2.4/hanadb1-data-mnt00001、nfs://10.32.2.4/hanadb1-log-mnt00001 等是 Azure NetApp 文件卷的文件路径。
    
    在 hanadb1 上

    - 卷 hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - 卷 hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - 卷 hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    在 hanadb2 上

    - 卷 hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - 卷 hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - 卷 hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>重要注意事项

在为 SAP HANA 纵向扩展系统创建 Azure NetApp 文件时，请注意以下事项：

- 最小容量池为 4 TiB。
- 最小卷大小为 100 GiB。
- Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)中。
- 所选的虚拟网络必须具有一个委派给 Azure NetApp 文件的子网。
- Azure NetApp 文件卷的吞吐量取决于卷配额和服务级别，如 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量满足 HANA 系统要求。
- 通过 Azure NetApp 文件[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以对允许的客户端、访问类型（读写、只读等）进行控制。
- Azure NetApp 文件功能尚无区域感知性。 目前，该功能未在 Azure 区域中的所有可用性区域中部署。 请注意某些 Azure 区域的潜在延迟影响。

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近位置部署。

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 文件卷的吞吐量取决于卷大小和服务级别，如 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。

在 Azure 中为 SAP 设计基础结构时，请注意 SAP 的一些最小存储要求，这可转化为最低吞吐量特征：

- I/O 大小为 1 MB 时，/hana/log 上的读/写速率为 250 兆字节/秒 (MB/s)。
- I/O 大小为 16 MB 和 64 MB 时，/hana/data 上的读取活动速率至少为 400 MB/s。
- I/O 大小为 16 MB 和 64 MB 时，/hana/data 上的写入活动速率至少为 250 MB/s。

每 1 TiB 的卷配额的 [Azure NetApp 文件吞吐量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)为：

- 高级存储层 - 64 MiB/s。
- 超高性能存储层 - 128 MiB/s。

为了符合 /hana/data 和 /hana/log 的 SAP 最低吞吐量要求以及 /hana/shared 准则，建议的大小为：

|    数据量(Volume)    | 高级存储层大小 | 超高性能存储层大小 | 支持的 NFS 协议 |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6.3 TiB            |          3.2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 或 v4.1    |


> [!NOTE]
> 此处所述的 Azure NetApp 文件大小调整建议旨在满足 SAP 向其基础结构提供商建议的最低要求。 在实际客户部署和工作负载方案中，这些大小可能还不够。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。

> [!TIP]
> 可以动态调整 Azure NetApp 文件卷的大小，而无需卸载卷、停止虚拟机或停止 SAP HANA。 此方法具有灵活性，可以满足应用程序的预期和不可预见的吞吐量需求。

> [!NOTE]
> 本文中用于装载 /hana/shared 的所有命令均为 NFSv4.1 /hana/shared 卷展示。
> 如果将 hana/shared 卷作为 NFSv3 卷部署，请不要忘记为 NFSv3 调整 /hana/shared 的装载命令。


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机 

首先，需要创建 Azure NetApp 文件卷。 然后执行以下步骤：

1.  创建资源组。
2.  创建虚拟网络。
3.  创建可用性集。 设置最大更新域。
4.  创建负载均衡器（内部）。 建议使用标准负载均衡器。
    选择在步骤 2 中创建的虚拟网络。
5.  创建虚拟机 1 (hanadb1)。 
6.  创建虚拟机 2 (hanadb2)。  
7.  创建虚拟机时，我们不会添加任何磁盘，因为所有装载点都将位于 Azure NetApp 文件的 NFS 共享上。 

> [!IMPORTANT]
> 负载均衡方案中的 NIC 辅助 IP 配置不支持浮动 IP。 有关详细信息，请参阅 [Azure 负载均衡器限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果你需要为 VM 提供其他 IP 地址，请部署第二个 NIC。    

> [!NOTE] 
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)。

8.  如果使用标准负载均衡器，请执行以下配置步骤：
    1.  首先创建前端 IP 池：
        1.  打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
        1.  输入新前端 IP 池的名称（例如 **hana-frontend**）。
        1.  将“分配”设置为“静态”并输入 IP 地址（例如，“10.32.0.10”）。
        1.  选择“确定”。
        1.  创建新前端 IP 池后，请记下池 IP 地址。
    1.  接下来创建后端池：
        1.  打开负载均衡器，选择 **后端池**，然后选择“添加”。
        1.  输入新后端池的名称（例如 **hana-backend**）。
        1.  选择“添加虚拟机”。
        1.  选择“虚拟机”。
        1.  选择 SAP HANA 群集的虚拟机及其 IP 地址。
        1.  选择 **添加**。
    1.  接下来创建运行状况探测：
        1.  打开负载均衡器，选择 **运行状况探测**，然后选择“添加”。
        1.  输入新运行状况探测的名称（例如 **hana-hp**）。
        1.  选择“TCP”作为协议，并选择端口 62503。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
        1.  选择“确定”。
    1.  接下来，创建负载均衡规则：
        1.  打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend、hana-backend 和 hana-hp）。
        1.  选择“HA 端口”。
        1.  确保 **启用浮动 IP**。
        1.  选择“确定”。


9. 或者，如果你的方案指示使用基本负载均衡器，请执行以下配置步骤：
    1.  配置负载均衡器。 首先创建前端 IP 池：
        1.  打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
        1.  输入新前端 IP 池的名称（例如 **hana-frontend**）。
        1.  将“分配”设置为“静态”并输入 IP 地址（例如，“10.32.0.10”）。
        1.  选择“确定”。
        1.  创建新前端 IP 池后，请记下池 IP 地址。
    1.  接下来创建后端池：
        1.  打开负载均衡器，选择 **后端池**，然后选择“添加”。
        1.  输入新后端池的名称（例如 **hana-backend**）。
        1.  选择“添加虚拟机”。
        1.  选择在步骤 3 中创建的可用性集。
        1.  选择 SAP HANA 群集的虚拟机。
        1.  选择“确定”。
    1.  接下来创建运行状况探测：
        1.  打开负载均衡器，选择 **运行状况探测**，然后选择“添加”。
        1.  输入新运行状况探测的名称（例如 **hana-hp**）。
        1.  选择“TCP”作为协议，并选择端口 625 **03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
        1.  选择“确定”。
    1.  对于 SAP HANA 1.0，请创建负载均衡规则：
        1.  打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb-3 **03** 15）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
        1.  将“协议”保留设置为“TCP”，输入端口 3 **03** 15。 
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保 **启用浮动 IP**。
        1.  选择“确定”。
        1.  针对端口 3 **03** 17 重复上述步骤。
    1.  对于 SAP HANA 2.0，请为系统数据库创建负载均衡规则：
        1.  打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb-3 **03** 13）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
        1.  将“协议”保留设置为“TCP”，输入端口 3 **03** 13。 
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保 **启用浮动 IP**。
        1.  选择“确定”。
        1.  针对端口 3 **03** 14 重复上述步骤。
    1.  对于 SAP HANA 2.0，请先为租户数据库创建负载均衡规则：
        1.  打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb-3 **03** 40）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
        1.  将“协议”保留设置为“TCP”，输入端口 3 **03** 40。 
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保 **启用浮动 IP**。
        1.  选择“确定”。
        1.  针对端口 3 **03** 41 和 3 **03** 42 重复上述步骤。

有关 SAP HANA 所需端口的详细信息，请参阅 [SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6)（SAP HANA 租户数据库）指南中的 [Connections to Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)（连接到租户数据库）章节或 SAP 说明 [2388694](https://launchpad.support.sap.com/#/notes/2388694)。

> [!IMPORTANT]
> 请勿在放置于 Azure 负载均衡器之后的 Azure VM 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数“net.ipv4.tcp_timestamps”设置为“0”。 有关详细信息，请参阅[负载均衡器运行状况探测](../../../load-balancer/load-balancer-custom-probe-overview.md)。 另请参阅 SAP 说明 [2382421](https://launchpad.support.sap.com/#/notes/2382421)。

## <a name="mount-the-azure-netapp-files-volume"></a>装载 Azure NetApp 文件卷

1. **[A]** 为 HANA 数据库卷创建装入点。 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** 验证 NFS 域设置。 确保域配置为默认的 Azure NetApp 文件域（即 defaultv4iddomain.com），并且映射设置为 nobody。

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > 确保在 VM 上的 /etc/idmapd.conf 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置 defaultv4iddomain.com。 如果 NFS 客户端（即 VM）上的域配置和 NFS 服务器（即 Azure NetApp 配置）之间不匹配，则 VM 上已装载的 Azure NetApp 卷上文件的权限将显示为 nobody。
    

3. **[1]** 在 node1 (hanadb1) 上装载特定于节点的卷 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** 在 node2 (hanadb2) 上装载特定于节点的卷
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** 验证是否使用 NFS 协议版本 NFSv4 装载了所有 HANA 卷。

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** 验证 nfs4_disable_idmapping。 应将其设置为“Y”。若要创建 nfs4_disable_idmapping 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   有关如何更改 nfs_disable_idmapping 参数的更多详细信息，请参阅 [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883)。 


## <a name="sap-hana-installation"></a>SAP HANA 安装

1. **[A]** 为所有主机设置主机名解析。

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts 文件。 此示例演示如何使用 /etc/hosts 文件。 请替换以下命令中的 IP 地址和主机名：

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

3. **[A]** 如 [NetApp SAP Applications on Microsoft Azure using Azure NetApp Files][anf-sap-applications-azure]（Microsoft Azure 上使用 Azure Files 文件的 Netapp SAP 应用程序）中所述，准备好 OS 以便在使用 NFS 的 Azure NetApp 上运行 SAP HANA。 为 NetApp 配置设置创建配置文件 /etc/sysctl.d/netapp-hana.conf。  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1    
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 使用其他优化设置创建配置文件 /etc/sysctl.d/ms-az.conf。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

    > [!TIP]
    > 请避免在 sysctl 配置文件中显式设置 net.ipv4.ip_local_port_range 和 net.ipv4.ip_local_reserved_ports，以允许 SAP 主机代理管理端口范围。 有关更多详细信息，请参阅 SAP 说明 [2382421](https://launchpad.support.sap.com/#/notes/2382421)。  

5. **[A]** 根据 [NetApp SAP Applications on Microsoft Azure using Azure NetApp Files][anf-sap-applications-azure]（Microsoft Azure 上使用 Azure NetApp 文件的 Netapp SAP 应用程序）中的建议调整 sunrpc 设置。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

2. **[A]** RHEL for HANA 配置

   根据 RHEL 版本，按照以下 SAP 说明所述配置 RHEL

   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
   - [2777782 - SAP HANA DB：建议用于 RHEL 8 的操作系统设置](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux：运行使用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux：运行使用 GCC 7.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux：运行使用 GCC 9.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** 安装 SAP HANA

   从 HANA 2.0 SPS 01 开始，MDC 是默认选项。 安装 HANA 系统时，将一起创建 SYSTEMDB 以及具有相同 SID 的租户。 在某些情况下，不需要默认租户。 如果不想在安装过程中创建初始租户，可以按照 SAP 说明 [2629711](https://launchpad.support.sap.com/#/notes/2629711) 进行操作

    从 HANA DVD 运行 hdblcm 程序。 在提示符下输入以下值：  
    选择安装：输入 1（用于安装）  
    选择要安装的其他组件：输入 **1**。  
    输入安装路径 [/hana/shared]：按 Enter 接受默认值  
    输入本地主机名 [..]：按 Enter 接受默认值  
    是否要将其他主机添加到系统? (y/n) [n]：n  
    输入 SAP HANA 系统 ID：输入 HN1。  
    输入实例编号 [00]：输入 03  
    选择数据库模式/输入索引 [1]：按 Enter 接受默认值  
    选择系统使用情况/输入索引 [4]：输入 4（用于自定义）  
    输入数据卷 [/hana/data] 的位置：按 Enter 接受默认值  
    输入日志卷 [/hana/log] 的位置：按 Enter 接受默认值  
    是否限制最大内存分配? [n]：按 Enter 接受默认值  
    输入主机“...”的证书主机名 [...]：按 Enter 接受默认值  
    输入 SAP 主机代理用户 (sapadm) 密码：输入主机代理用户密码  
    确认 SAP 主机代理用户 (sapadm) 密码：再次输入主机代理用户密码以确认  
    输入系统管理员 (hn1adm) 密码：输入系统管理员密码  
    确认系统管理员 (hn1adm) 密码：再次输入系统管理员密码以确认  
    输入系统管理员主目录 [/usr/sap/HN1/home]：按 Enter 接受默认值  
    输入系统管理员登录 Shell [/bin/sh]：按 Enter 接受默认值  
    输入系统管理员用户 ID [1001]：按 Enter 接受默认值  
    输入用户组 (sapsys) ID [79]：按 Enter 接受默认值  
    输入数据库用户 (SYSTEM) 密码：输入数据库用户密码  
    确认数据库用户 (SYSTEM) 密码：再次输入数据库用户密码以确认  
    重新引导计算机后是否重新启动系统? [n]：按 Enter 接受默认值  
    是否继续? (y/n)：验证摘要。 输入 y 继续  

4. [A] 升级 SAP 主机代理

   从 [SAP 软件中心](https://launchpad.support.sap.com/#/softwarecenter)下载最新的 SAP 主机代理存档，并运行以下命令来升级代理。 替换存档的路径，使其指向已下载的文件：

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** 配置防火墙

   为 Azure 负载均衡器探测端口创建防火墙规则。

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>配置 SAP HANA 系统复制

按照设置 [SAP HANA 系统复制](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication)中的步骤配置 SAP HANA 系统复制。 

## <a name="cluster-configuration"></a>群集配置

本部分介绍使用 Azure NetApp 文件在 NFS 共享上安装 SAP HANA 时群集无缝运行所需的必要步骤。 

### <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 的步骤，创建适用于此 HANA 服务器的基本 Pacemaker 群集。

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>实现 Python 系统复制挂钩 SAPHanaSR

这是优化与群集的集成并在需要进行群集故障转移时改进检测的重要步骤。 强烈建议配置 SAPHanaSR python 挂钩。    

1. [A] 安装 HANA“系统复制挂钩”。 需要在两个 HANA DB 节点上安装挂钩。           

   > [!TIP]
   > 只能对 HANA 2.0 实现 python 挂钩。        

   1. 以 `root` 身份准备挂钩。  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 在两个节点上停止 HANA。 以 <sid\>adm 身份执行：  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. 在每个群集节点上调整 `global.ini`。  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. [A] 群集需要在每个群集节点上为 <sid\>adm 配置 sudoers。 在此示例中，通过创建新文件来实现此目的。 以 `root` 身份执行命令。    
    ```bash
    sudo visudo -f /etc/sudoers.d/20-saphana
    # Insert the following lines and then save
    Cmnd_Alias SITE1_SOK   = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE1 -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE1_SFAIL = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE1 -v SFAIL -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE2_SOK   = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE2 -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE2_SFAIL = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE2 -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SITE1_SOK, SITE1_SFAIL, SITE2_SOK, SITE2_SFAIL
    Defaults!SITE1_SOK, SITE1_SFAIL, SITE2_SOK, SITE2_SFAIL !requiretty
    ```

3. [A] 在两个节点上启动 SAP HANA。 以 <sid\>adm 身份执行。  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** 验证是否安装了挂钩。 在活动 HANA 系统复制站点上以 <sid\>adm 的身份执行。   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

有关 SAP HANA 系统复制挂钩实现的更多详细信息，请参阅[启用 SAP HA/DR 提供程序挂钩](https://access.redhat.com/articles/3004101#enable-srhook)。  

### <a name="configure-filesystem-resources"></a>配置文件系统资源

在此示例中，每个群集节点都有其自己的 HANA NFS 文件系统 /hana/shared、/hana/data 和 /hana/log。   

1. **[1]** 将群集置于维护模式。

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** 为 hanadb1 装载创建文件系统资源。

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** 为 hanadb2 装载创建文件系统资源。

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` 属性会添加到监视操作，以便每个监视器在文件系统上执行读/写测试。 如果没有此属性，则监视操作仅验证是否已装载文件系统。 这可能是一个问题，因为连接丢失时，尽管无法访问文件系统，但它仍可能保持装载状态。

    `on-fail=fence` 属性也会添加到监视操作中。 使用此选项时，如果监视操作在节点上失败，则会立即隔离该节点。 如果不使用此选项，则默认行为是停止依赖于失败资源的所有资源，然后重启失败的资源，再启动依赖于失败资源的所有资源。 当 SAPHana 资源依赖于失败的资源时，此行为不仅费时，还可能彻底失败。 如果持有 HANA 可执行文件的 NFS 服务器不可访问，则 SAPHana 资源无法成功停止。

4. **[1]** 配置位置约束

   配置位置约束，以确保管理 hanadb1 唯一装载的资源绝不会在 hanadb2 上运行，反之亦然。

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    设置 `resource-discovery=never` 选项是因为每个节点的唯一装载共享相同的装入点。 例如，`hana_data1` 使用装入点 `/hana/data`，而 `hana_data2` 也使用装入点 `/hana/data`。 如果在群集启动时检查资源状态，这可能导致探测操作误报，进而可能导致不必要的恢复行为。 可以通过设置 `resource-discovery=never` 来避免此现象

5. **[1]** 配置属性资源

   配置属性资源。 如果已装载节点的所有 NFS 装载（/hana/data、/hana/log 和 /hana/data），则这些属性将设置为 true，否则将设置为 false。

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** 配置位置约束

   配置位置约束，以确保 hanadb1 的属性资源绝不会在 hanadb2 上运行，反之亦然。

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** 创建排序约束

   配置排序约束，以便仅在装载了节点的所有 NFS 装载之后，才启动节点的属性资源。
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > 如果配置包括组 `hanadb1_nfs` 或 `hanadb2_nfs` 之外的文件系统，则包括 `sequential=false` 选项，以使文件系统之间没有排序依赖关系。 所有文件系统都必须在 `hana_nfs1_active` 之前启动，但它们不需要以相对于彼此的任何顺序启动。 有关更多详细信息，请参阅 [How do I configure SAP HANA System Replication in Scale-Up in a Pacemaker cluster when the HANA filesystems are on NFS shares](https://access.redhat.com/solutions/5156571)（当 HANA 文件系统位于 NFS 共享上时，如何在 Pacemaker 群集中配置 SAP HANA 纵向扩展系统复制）

### <a name="configure-sap-hana-cluster-resources"></a>配置 SAP HANA 群集资源

1. 按照[创建 SAP HANA 群集资源](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources)中的步骤在群集中创建 SAP HANA 资源。 创建 SAP HANA 资源后，我们需要在 SAP HANA 资源和文件系统（NFS 装载）之间创建位置规则约束

2. **[1]** 配置 SAP HANA 资源和 NFS 装载之间的约束

   我们将设置位置规则约束，以便仅在装载了节点的所有 NFS 装载后，SAP HANA 资源才能在该节点上运行。

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   检查群集和所有资源的状态
   > [!NOTE]
   > 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>在 Pacemaker 群集中配置启用 HANA 活动/读取的系统复制

从 SAP HANA 2.0 SPS 01 开始，SAP 允许对 SAP HANA 系统复制使用启用活动/读取的设置，在这种情况下，SAP HANA 系统复制的辅助系统可积极用于读取密集型工作负载。 若要在群集中支持此类设置，需要提供第二个虚拟 IP 地址，以便客户端能够访问启用辅助启用读取的 SAP HANA 数据库。 若要确保辅助复制站点在接管后仍可以访问，群集需要将虚拟 IP 地址与 SAPHana 资源的辅助地址一起移动。

[在 Pacemaker 群集中配置启用 HANA 活动/读取的系统复制](./sap-hana-high-availability-rhel.md#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)中介绍了在具有第二个虚拟 IP 的 Red Hat 高可用性群集中管理启用 HANA 活动/读取的系统复制所需的其他配置。  

在继续下一步之前，请确保已完全配置管理 SAP HANA 数据库的 Red Hat 高可用性群集，如上文所述。    


## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试设置。 

1. 在开始测试之前，请确保 Pacemaker 没有任何失败的操作（通过 pcs status）、没有任何意外的位置约束（例如迁移测试的遗留设置），且 HANA 系统复制处于同步状态（例如，使用 systemReplicationStatus）：

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. 验证节点失去 NFS 共享 (/hana/shared) 访问权限的故障方案中的群集配置

   SAP HANA 资源代理依赖于二进制文件，这些文件存储在 `/hana/shared` 中，以便在故障转移过程中执行操作。 在所示方案中，文件系统 `/hana/shared` 已通过 NFS 装载。  
   当其中一台服务器失去 NFS 共享访问权限时，很难模拟故障。 可以执行的测试是以只读方式重新装载文件系统。
   此方法验证在活动节点上失去 `/hana/shared` 访问权限时，群集是否能够进行故障转移。     


   **预期结果**：将 `/hana/shared` 设为只读文件系统时，在文件系统上执行读/写操作的资源 `hana_shared1` 的 `OCF_CHECK_LEVEL` 属性将失败（因为它无法在文件系统上写入任何内容），并将执行 HANA 资源故障转移。  当 HANA 节点失去 NFS 共享访问权限时，会出现相同的结果。 

   开始测试之前的资源状态：

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   可以使用以下命令在活动群集节点上将 /hana/shared 置于只读模式：

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 将根据 stonith (`pcs property show stonith-action`) 上设置的操作重新引导或关闭。  服务器 (hanadb1) 关闭后，HANA 资源将移至 hanadb2。 可以从 hanadb2 检查群集的状态。

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   我们建议通过执行[在 RHEL 上设置 SAP HANA 系统复制](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)中所述的测试来全面测试 SAP HANA 群集配置。

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)