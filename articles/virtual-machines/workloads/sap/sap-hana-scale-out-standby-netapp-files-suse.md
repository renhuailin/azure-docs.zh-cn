---
title: 使用 SLES 上的 Azure NetApp 文件部署具有备用节点的 SAP HANA 横向扩展系统 | Microsoft Docs
description: 了解如何使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统。
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: radeltch
ms.openlocfilehash: a4e3d2b2c6b7c56771ebfddd60bb5ec13ab78d28
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643624"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:/azure/azure-netapp-files/
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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文介绍如何将 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)用于共享存储卷，在 Azure 虚拟机 (VM) 上部署具有备用节点的、采用横向扩展配置的高可用性 SAP HANA 系统。  

在示例配置和安装命令等中，HANA 实例为 03，HANA 系统 ID 为 HN1 。 这些示例基于 HANA 2.0 SP4 和 SUSE Linux Enterprise Server for SAP 12 SP4。 

在开始之前，请参阅以下 SAP 说明和文章：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明 [1928533] 包括：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明 [2015553]：列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件
* SAP 说明 [2205917]：包含适用于 SUSE Linux Enterprise Server for SAP Applications 的推荐 OS 设置
* SAP 说明 [1944799]：包含适用于 SUSE Linux Enterprise Server for SAP Applications 的 SAP 准则
* SAP 说明 [2178632]：包含有关在 Azure 中为 SAP 报告的所有监视指标的详细信息
* SAP 说明 [2191498]：包含 Azure 中的 Linux 所需的 SAP 主机代理版本
* SAP 说明 [2243692]：包含 Azure 中 Linux 上的 SAP 许可的相关信息
* SAP 说明 [1984787]：包含有关 SUSE Linux Enterprise Server 12 的一般信息
* SAP 说明 [1999351]：包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息
* SAP 说明 [1900823]：包含有关 SAP HANA 存储要求的信息
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含适用于 Linux 的所有必需 SAP 说明
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳做法指南][suse-ha-guide]：包含在本地设置 NetWeaver 高可用性以及 SAP HANA 系统复制的所有必需信息（用作一般基线，可提供更详细的信息）
* [SUSE 高可用性扩展 12 SP3 发行说明][suse-ha-12sp3-relnotes]
* [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]
* [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)

## <a name="overview"></a>概述

实现 HANA 高可用性的一种方法是配置主机自动故障转移。 若要配置主机自动故障转移，请将一个或多个虚拟机添加到 HANA 系统，并将其配置为备用节点。 当活动节点发生故障时，备用节点会自动接管。 在使用 Azure 虚拟机提供的配置中，可以使用 [Azure NetApp 文件上的 NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md) 来实现自动故障转移。  

> [!NOTE]
> 备用节点需要访问所有数据库卷。 HANA 卷必须装载为 NFSv4 卷。 NFSv4 协议中经过改进的基于文件租约的锁定机制用于 `I/O` 隔离。 

> [!IMPORTANT]
> 若要生成受支持的配置，必须将 HANA 数据卷和日志卷部署为 NFSv4.1 卷，并使用 NFSv4.1 协议装载这些卷。 具有备用节点的 HANA 主机自动故障转移配置不支持 NFSv3。

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

在上图中，按照 SAP HANA 网络建议，一个 Azure 虚拟网络中表示了三个子网： 
* 用于客户端通信
* 用于与存储系统通信
* 用于内部 HANA 节点间通信

Azure NetApp 卷部署在[委派给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)的单独子网中。  

对于此示例配置，子网为：  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>设置 Azure NetApp 文件基础结构 

在继续设置 Azure NetApp 文件基础结构之前，请先通过 [Azure NetApp 文件文档][anf-azure-doc]熟悉该基础结构。 

Azure NetApp 文件在多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 查看所选 Azure 区域是否提供 Azure NetApp 文件。  

有关 Azure NetApp 文件在各 Azure 区域的可用性的信息，请参阅[按 Azure 区域划分的 Azure NetApp 文件可用性][anf-avail-matrix]。  

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和 VM（将装载 Azure NetApp 文件资源）必须部署在同一 Azure 虚拟网络或对等 Azure 虚拟网络中。  

1. 按照[创建 NetApp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的说明，在所选的 Azure 区域中创建一个 NetApp 帐户。  

2. 按照[设置 Azure NetApp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明，设置 Azure NetApp 文件容量池。  

   本文中介绍的 HANA 体系结构使用超高性能服务级别的单个 Azure NetApp 文件容量池。 对于 Azure 上的 HANA 工作负载，建议使用 Azure NetApp 文件超高性能或高级[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。  

3. 按照[将子网委派给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明所述，将子网委派给 Azure NetApp 文件。  

4. 按照[为 Azure NetApp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明部署 Azure NetApp 文件卷。  

   部署卷时，请务必选择“NFSv4.1”版本。 目前，需要将 NFSv4.1 访问权限添加到允许列表。 将卷部署在指定的 Azure NetApp 文件[子网](/rest/api/virtualnetwork/subnets)中。 将自动分配 Azure NetApp 卷的 IP 地址。 
   
   请记住，Azure NetApp 文件资源和 Azure VM 必须位于同一 Azure 虚拟网络或对等 Azure 虚拟网络中。 例如，HN1-data-mnt00001、HN1-log-mnt00001 等是卷名称，nfs://10.23.1.5/HN1-data-mnt00001、nfs://10.23.1.4/HN1-log-mnt00001 等是 Azure NetApp 文件卷的文件路径   。  

   * 卷 HN1-data-mnt00001 (nfs://10.23.1.5/HN1-data-mnt00001) 
   * 卷 HN1-data-mnt00002 (nfs://10.23.1.6/HN1-data-mnt00002) 
   * 卷 HN1-log-mnt00001 (nfs://10.23.1.4/HN1-log-mnt00001) 
   * 卷 HN1-log-mnt00002 (nfs://10.23.1.6/HN1-log-mnt00002) 
   * 卷 HN1-shared (nfs://10.23.1.4/HN1-shared) 
   
   在此示例中，我们对每个 HANA 数据卷和日志卷使用了单独的 Azure NetApp 文件卷。 要在较小或非生产系统上采用成本优化程度更高的配置，可将所有数据装入点和所有日志装入点放到一个卷上。  

### <a name="important-considerations"></a>重要注意事项

在为 SUSE 高可用性体系结构上的 SAP NetWeaver 创建 Azure NetApp 文件时，请注意以下重要事项：

- 最小容量池为 4 TiB。  
- 最小卷大小为 100 GiB。
- Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)中。  
- 所选的虚拟网络必须具有一个委派给 Azure NetApp 文件的子网。
- Azure NetApp 文件卷的吞吐量取决于卷配额和服务级别，如 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量满足 HANA 系统要求。  
- 通过 Azure NetApp 文件[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以对允许的客户端、访问类型（读写、只读等）进行控制。 
- Azure NetApp 文件功能尚没有区域感知性。 目前，该功能未在 Azure 区域中的所有可用性区域中部署。 请注意某些 Azure 区域的潜在延迟影响。  
-  

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近的地方部署。  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 文件卷的吞吐量取决于卷大小和服务级别，如 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

在 Azure 中为 SAP 设计基础结构时，请注意 SAP 的一些最小存储要求，这可转化为最低吞吐量特征：

- 在 I/O 大小为 1-MB、速率为 250 MB/秒的 /hana/log 上启用读/写。  
- 为 16-MB 和 64-MB I/O 大小的 /hana/data 启用至少 400 MB/秒的读取活动。  
- 为 16-MB 和 64-MB I/O 大小的 /hana/data 启用至少 250 MB/秒的写入活动。 

每 1 TiB 的卷配额的 [Azure NetApp 文件吞吐量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)为：
- 高级存储层 - 64 MiB/s  
- 超高性能存储层 - 128 MiB/s  

为了符合数据和日志的 SAP 最低吞吐量要求以及 /hana/shared 准则，建议的大小为：

| 数据量(Volume) | 大小<br>高级存储层 | 大小<br>超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 每 4 个工作器节点的最大值（512 GB，1xRAM） | 每 4 个工作器节点的最大值（512 GB，1xRAM） | v3 或 v4.1 |

本文中所述的使用 Azure NetApp 文件超高性能存储层的布局的 SAP HANA 配置为：

| 数据量(Volume) | 大小<br>超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 或 v4.1 |

> [!NOTE]
> 此处所述的 Azure NetApp 文件大小调整建议旨在满足 SAP 向其基础结构提供商建议的最低要求。 在实际客户部署和工作负载方案中，这些大小可能还不够。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。  

> [!TIP]
> 可以动态调整 Azure NetApp 文件卷的大小，而无需卸载卷、停止虚拟机或停止 SAP HANA。 此方法具有灵活性，可以满足应用程序的预期和不可预见的吞吐量需求。

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机

首先，需要创建 Azure NetApp 文件卷。 然后执行以下步骤：
1. 在 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)中创建 [Azure 虚拟网络子网](../../../virtual-network/virtual-network-manage-subnet.md)。 
1. 部署 VM。 
1. 创建更多网络接口，并将这些网络接口附加到相应的 VM。  

   每个虚拟机有三个网络接口，分别对应于三个 Azure 虚拟网络子网（`client`、`storage` 和 `hana`）。 

   有关详细信息，请参阅[如何在 Azure 中创建具有多个网络接口卡的 Linux 虚拟机](../../linux/multiple-nics.md)。  

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 若要实现低延迟，请与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近位置部署。 [加入使用 SAP HANA Azure NetApp 文件的新 SAP HANA 系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)时，请提交所需的信息。 
 
接下来的说明假设已创建资源组、Azure 虚拟网络和三个 Azure 虚拟网络子网：`client`、`storage` 和 `hana`。 部署 VM 时，请选择 client 子网，使客户端网络接口成为 VM 上的主要接口。 此外，还需要配置一个通过 storage 子网网关到达 Azure NetApp 文件委派子网的显式路由。 

> [!IMPORTANT]
> 请确保你选择的 OS 已通过 SAP 针对你使用的特定 VM 类型上的 SAP HANA 进行的认证。 有关经 SAP HANA 认证的 VM 类型和这些类型的 OS 版本列表，请访问[经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)网站。 单击进入列出的 VM 类型的详细信息，获取该类型的 SAP HANA 支持的 OS 版本完整列表。  

1. 为 SAP HANA 创建可用性集。 请确保设置最大更新域。  

2. 执行以下步骤创建三个虚拟机（hanadb1、hanadb2、hanadb3）  ：  

   a. 使用 Azure 库中受 SAP HANA 支持的 SLES4SAP 映像。 在此示例中，我们使用了 SLES4SAP 12 SP4 映像。  

   b. 选择前面为 SAP HANA 创建的可用性集。  

   c. 选择 client Azure 虚拟网络子网。 选择[加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md)。  

   部署虚拟机时，系统会自动生成网络接口名称。 在这些说明中，为方便起见，我们将自动生成的、已附加到 client Azure 虚拟网络子网的网络接口称为 hanadb1-client、hanadb2-client 和 hanadb3-client  。 

3. 为 `storage` 虚拟网络子网创建三个网络接口（为每个虚拟机各创建一个，在此示例中分别为 hanadb1-storage、hanadb2-storage 和 hanadb3-storage）  。  

4. 为 `hana` 虚拟网络子网创建三个网络接口（为每个虚拟机各创建一个，在此示例中分别为 hanadb1-hana、hanadb2-hana 和 hanadb3-hana）  。  

5. 执行以下步骤，将新建的虚拟网络接口附加到相应的虚拟机：  

    a. 转到 [Azure 门户](https://portal.azure.com/#home)中的虚拟机。  

    b. 在左侧窗格中，选择“虚拟机”。 按虚拟机名称（例如 hanadb1）进行筛选，然后选择该虚拟机。  

    c. 在“概述”窗格中，选择“停止”以解除分配虚拟机。  

    d. 选择“网络”，然后附加网络接口。 在“附加网络接口”下拉列表中，选择已为 `storage` 和 `hana` 子网创建的网络接口。  
    
    e. 选择“保存”。  
 
    f. 对剩余的虚拟机（在此示例中为 hanadb2 和 hanadb3）重复步骤 b 到 e 。
 
    g. 使虚拟机暂时处于停止状态。 接下来，我们将为所有新附加的网络接口启用[加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md)。  

6. 通过执行以下步骤，为 `storage` 和 `hana` 子网的其他网络接口启用加速网络：  

    a. 在 [Azure 门户](https://portal.azure.com/#home)中打开 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。  

    b. 执行以下命令，为附加到 `storage` 和 `hana` 子网的其他网络接口启用加速网络。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 执行以下步骤启动虚拟机：  

    a. 在左侧窗格中，选择“虚拟机”。 按虚拟机名称（例如 hanadb1）进行筛选，然后选择该虚拟机。  

    b. 在“概述”窗格中选择“启动” 。  

## <a name="operating-system-configuration-and-preparation"></a>操作系统配置和准备

后续部分中的说明带有以下前缀之一：
* **[A]** ：适用于所有节点
* **[1]** ：仅适用于节点 1
* **[2]** ：仅适用于节点 2
* **[3]** ：仅适用于节点 3

执行以下步骤配置并准备 OS：

1. **[A]** 维护虚拟机上的主机文件。 包括所有子网的条目。 在此示例中，已向 `/etc/hosts` 中添加了以下条目。  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. [A] 更改用于存储的网络接口的 DHCP 和云配置设置，以免发生意外的主机名更改。  

    以下说明假设存储网络接口为 `eth1`。 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. [A] 添加一个网络路由，以便能够通过存储网络接口来与 Azure NetApp 文件通信。  

    以下说明假设存储网络接口为 `eth1`。  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    重新启动 VM 以激活更改。  

3. [A] 如 [NetApp SAP Applications on Microsoft Azure using Azure NetApp Files][anf-sap-applications-azure]（Microsoft Azure 上使用 Azure Files 文件的 Netapp SAP 应用程序）中所述，准备好 OS 以便在使用 NFS 的 NetApp 系统上运行 SAP HANA。 为 NetApp 配置设置创建配置文件 /etc/sysctl.d/netapp-hana.conf。  

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
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. [A] 在 Microsoft 的配合下为 Azure 配置设置创建配置文件 /etc/sysctl.d/ms-az.conf。  

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

4. [A] 根据[NetApp SAP Applications on Microsoft Azure using Azure NetApp Files][anf-sap-applications-azure]（Microsoft Azure 上使用 Azure Files 文件的 Netapp SAP 应用程序）中的建议调整 sunrpc 设置。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>装载 Azure NetApp 文件卷

1. **[A]** 为 HANA 数据库卷创建装入点。  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** 为 HN1-shared 上的 /usr/sap 创建节点特定的目录。  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 验证 NFS 域设置。 请确保域配置为默认的 Azure NetApp 文件域（即“`defaultv4iddomain.com`”），并且映射设置为“无人”。  

    > [!IMPORTANT]
    > 确保在 VM 上的 `/etc/idmapd.conf` 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置：“`defaultv4iddomain.com`”。 如果 NFS 客户端（即 VM）上的域配置和 NFS 服务器（即 Azure NetApp 配置）之间不匹配，则 VM 上已装载的 Azure NetApp 卷上文件的权限将显示为 `nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** 验证 `nfs4_disable_idmapping`。 它应设置为“Y”。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. [A] 手动创建 SAP HANA 组和用户。 组 sapsys 和用户 hn1adm 的 ID 必须设置为在加入期间提供的相同 ID。 （在此示例中，ID 设置为“1001”。）如果未正确设置 ID，将无法访问卷。 组 sapsys 以及用户帐户 hn1adm 和 sapadm 的 ID 必须在所有虚拟机上相同。  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. [A] 装载共享的 Azure NetApp 文件卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** 在 hanadb1 上装载节点特定的卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 在 hanadb2 上装载节点特定的卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 在 hanadb3 上装载节点特定的卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 验证是否使用 NFS 协议版本 NFSv4 装载了所有 HANA 卷。  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>安装  

在此示例中，我们已使用 HANA 2.0 SP4 在 Azure 中部署了具有备用节点、采用横向扩展配置的 SAP HANA。  

### <a name="prepare-for-hana-installation"></a>准备 HANA 安装

1. **[A]** 在安装 HANA 之前，请设置根密码。 可以在安装完成后禁用根密码。 以 `root` 身份执行命令 `passwd`。  

2. **[1]** 确保可以通过 SSH 登录到 hanadb2 和 hanadb3，并且登录过程中不会提示输入密码 。  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安装 HANA 2.0 SP4 所需的其他包。 有关详细信息，请参阅 SAP 说明 [2593824](https://launchpad.support.sap.com/#/notes/2593824)。 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. [2]、[3] 将 SAP HANA `data` 和 `log` 目录的所有权更改为 hn1adm 。   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA 安装

1. **[1]** 按照 [SAP HANA 2.0 Installation and Update guide](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)（SAP HANA 2.0 安装和更新指南）中的说明安装 SAP HANA。 在此示例中，我们将安装包含主节点、一个工作器节点和一个备用节点的 SAP HANA 横向扩展系统。  

   a. 从 HANA 安装软件目录启动 hdblcm 程序。 使用 `internal_network` 参数并传递子网的地址空间，用于内部 HANA 节点间通信。  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. 在提示符处输入以下值：

     * 对于“选择操作”：输入 1（用于安装）
     * 对于“要安装的其他组件”：输入 2、3 
     * 对于安装路径：按 Enter（默认为 /hana/shared）
     * 对于“本地主机名”：按 Enter 接受默认值
     * 在“是否要将主机添加到系统?”下：输入 y 
     * 对于“要添加的逗号分隔主机名”：输入 hanadb2, hanadb3 
     * 对于“Root 用户名 [root]”：按 Enter 接受默认值
     * 对于“Root 用户密码”：输入 root 用户的密码
     * 对于“主机 hanadb2 的角色”：输入 1（表示辅助角色）
     * 对于主机 hanadb2 的“主机故障转移组”[默认值]：按 Enter 接受默认值
     * 对于主机 hanadb2 的“存储分区号”[\<\<assign automatically\>\>]：按 Enter 接受默认值
     * 对于主机 hanadb2 的“辅助角色组”[默认值]：按 Enter 接受默认值
     * 对于主机 hanadb3 的“选择角色”：输入 2（表示备用角色） 
     * 对于主机 hanadb3 的“主机故障转移组”[默认值]：按 Enter 接受默认值
     * 对于主机 hanadb3 的“辅助角色组”[默认值]：按 Enter 接受默认值
     * 对于“SAP HANA 系统 ID”：输入 HN1
     * 对于“实例号”[00]：输入 03
     * 对于“本地主机辅助角色组”[默认值]：按 Enter 接受默认值
     * 对于“选择系统使用情况/输入索引[4]”：输入 4（用于自定义）
     * 对于“数据卷的位置”[/hana/data/HN1]：按 Enter 接受默认值
     * 对于“日志卷的位置”[/hana/log/HN1]：按 Enter 接受默认值
     * 对于“是否限制最大内存分配？” [n]：输入 n
     * 对于“主机 hanadb1 的证书主机名”[hanadb1]：按 Enter 接受默认值
     * 对于“主机 hanadb2 的证书主机名”[hanadb2]：按 Enter 接受默认值
     * 对于“主机 hanadb3 的证书主机名”[hanadb3]：按 Enter 接受默认值
     * 对于“系统管理员(hn1adm)密码”：输入密码
     * 对于“系统数据库用户(system)密码”：输入系统的密码
     * 对于“确认系统数据库用户(system)密码”：输入系统的密码
     * 对于“重新引导计算机后是否重启系统?” [n]：输入 n 
     * 对于“是否要继续(y/n)”：验证摘要，如果一切正常，请输入 y


2. **[1]** 验证 global.ini  

   显示 global.ini，并确保 SAP HANA 内部节点间通信的配置已就位。 验证 communication 节。 它应该具有 `hana` 子网的地址空间，并且 `listeninterface` 应设置为 `.internal`。 验证 internal_hostname_resolution 节。 它应该具有属于 `hana` 子网的 HANA 虚拟机的 IP 地址。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 添加主机映射，以确保使用客户端 IP 地址进行客户端通信。 添加 `public_host_resolution` 节，并添加 client 子网中的相应 IP 地址。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** 重启 SAP HANA 以激活更改。  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 验证客户端接口是否使用 `client` 子网中的 IP 地址进行通信。  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   有关如何验证配置的信息，请参阅 SAP 说明 [2183363 - SAP HANA 内部网络的配置](https://launchpad.support.sap.com/#/notes/2183363)。  

6. 若要为基础 Azure NetApp 文件存储优化 SAP HANA，请设置以下 SAP HANA 参数：

   - `max_parallel_io_requests` **128**
   - `async_read_submit` on **on**
   - `async_write_submit_active` on
   - `async_write_submit_blocks` **all**

   有关详细信息，请参阅 [NetApp SAP Applications on Microsoft Azure using Azure NetApp Files][anf-sap-applications-azure]（Microsoft Azure 上使用 Azure NetApp 文件的 NetApp SAP 应用程序）。 

   从 SAP HANA 2.0 系统开始，可以在 `global.ini` 中设置参数。 有关详细信息，请参阅 SAP 说明 [1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   
   对于 SAP HANA 1.0 系统版本 SPS12 和更低版本，可以按照 SAP 说明 [2267798](https://launchpad.support.sap.com/#/notes/2267798) 中所述，在安装期间设置这些参数。  

7. Azure NetApp 文件所用存储的文件大小限制为 16 TB。 SAP HANA 对存储限制没有内隐意识，达到 16 TB 的文件大小限制时，它不会自动创建新的数据文件。 随着 SAP HANA 不断尝试将文件增大至 16 TB 以上，这种尝试将导致出错，并最终导致索引服务器故障。 

   > [!IMPORTANT]
   > 为了防止 SAP HANA 尝试将数据文件增大至超过存储子系统的 [16-TB 限制](../../../azure-netapp-files/azure-netapp-files-resource-limits.md)，请在 `global.ini` 中设置以下参数。  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000。有关详细信息，请参阅 SAP 说明 [2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 请留意 SAP 说明 [2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>测试 SAP HANA 故障转移 

> [!NOTE]
> 本文包含对术语“主”和“从”的引用，Microsoft 不再使用这些术语 。 从软件中删除这些术语后，我们会将其从本文中删除。

1. 模拟 SAP HANA 工作器节点上的节点崩溃。 请执行以下操作： 

   a. 在模拟节点故障之前，请以 hn1adm 身份运行以下命令，以捕获环境的状态：  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. 若要模拟节点故障，请在工作器节点（在本例中为 hanadb2）上以 root 身份运行以下命令：  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 监视系统的故障转移完成状态。 故障转移完成后，请捕获状态。状态应如下所示：  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 如果节点遇到内核 panic，请在所有 HANA 虚拟机上将 `kernel.panic` 设置为 20 秒，以避免 SAP HANA 故障转移出现延迟。 在 `/etc/sysctl` 中完成配置。 重新引导虚拟机以激活更改。 如果不执行此更改，当某个节点遇到内核 panic 时，故障转移可能需要 10 分钟或更长时间。  

2. 通过执行以下操作来终止名称服务器：

   a. 在测试之前，以 hn1adm 身份运行以下命令，以检查环境的状态：  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. 在活动主节点（在本例中为 hanadb1）上以 hn1adm 身份运行以下命令 ：  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    备用节点 hanadb3 将接管为主节点。 下面是完成故障转移测试后的资源状态：  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. 在 hanadb1 上（即，在其名称服务器已终止的同一个虚拟机上）重启 HANA 实例。 hanadb1 节点将重新加入环境，并保留其备用角色。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在 hanadb1 上启动 SAP HANA 后，预期会显示以下状态：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. 同样，在当前处于活动状态的主节点上（即，在节点 hanadb3 上）终止名称服务器。  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   节点 hanadb1 将恢复主节点角色。 故障转移测试完成后，状态将如下所示：

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. 在已准备好充当备用节点的 hanadb3 上启动 SAP HANA。  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在 hanadb3 上启动 SAP HANA 后，状态将如下所示：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)
* 若要了解如何在 Azure VM 上建立 SAP HANA 的高可用性和灾难恢复计划，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 的高可用性][sap-hana-ha]。
