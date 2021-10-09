---
title: 在 SLES 上用 HSR 和 Pacemaker 进行 SAP HANA 横向扩展 | Microsoft Docs
description: 在 SLES 上用 HSR 和 Pacemaker 进行 SAP HANA 横向扩展。
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/26/2021
ms.author: radeltch
ms.openlocfilehash: ab66bcdc834fd894d7d258ab9c7c08a0049b07ed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673134"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>在 SUSE Linux Enterprise Server 上使用 HSR 实现 SAP HANA 横向扩展系统的高可用性 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:../../../azure-netapp-files/index.yml
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


本文介绍如何在 Azure SUSE Linux Enterprise Server 虚拟机 (VM) 上使用 HANA 系统复制 (HSR) 和 Pacemaker 在横向扩展配置中部署高可用性 SAP HANA 系统。 所提供体系结构中的共享文件系统由 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)提供，并通过 NFS 进行装载。  

在示例配置和安装命令等中，HANA 实例为 03，HANA 系统 ID 为 HN1 。 这些示例基于 HANA 2.0 SP4 和 SUSE Linux Enterprise Server 12 SP5。 

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
* [SUSE 高可用性扩展 12 SP5 发行说明](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [ HANA 系统复制处理 SUSE HA 群集中失败的 NFS 共享](https://www.suse.com/support/kb/doc/?id=000019904)
* [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]
* [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)

## <a name="overview"></a>概述

为 HANA 横向扩展安装实现 HANA 高可用性的一种方法是配置 HANA 系统复制，并通过 Pacemaker 群集保护解决方案，以允许自动故障转移。 当活动节点发生故障时，群集会将 HANA 资源故障转移到另一个站点。  
提供的配置显示每个站点上有三个 HANA 节点，以及用于防止拆分式方案的多数仲裁节点。 可以改编说明，以将更多 VM 作为 HANA DB 节点。  

所提供体系结构中的 HANA 共享文件系统 `/hana/shared` 由 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)提供。 它通过 NFSv4.1 在同一 HANA 系统复制站点中的每个 HANA 节点上装载。 文件系统 `/hana/data` 和 `/hana/log` 是本地文件系统，不在 HANA DB 节点之间共享。 SAP HANA 将以非共享模式安装。 

> [!TIP]
> 有关建议的 SAP HANA 存储配置，请参阅 [SAP HANA Azure VM 存储配置](./hana-vm-operations-storage.md)。   

[![在 SLES 上使用 HSR 和 Pacemaker 群集进行 SAP HANA 横向扩展](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

上图中，按照 SAP HANA 网络建议，一个 Azure 虚拟网络中表示了三个子网： 
* 对于客户端通信 - `client` 10.23.0.0/24  
* 对于内部 HANA 节点间通信 - `inter` 10.23.1.128/26  
* 对于 HANA 系统复制 - `hsr` 10.23.1.192/26  

由于 `/hana/data` 和 `/hana/log` 部署在本地磁盘上，因此不必部署单独的子网和单独的虚拟网卡来与存储进行通信。  

Azure NetApp 卷部署在一个单独的子网中，[已委托给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)`anf` 10.23.1.0/26。   

> [!IMPORTANT]
> 不支持将系统复制到第三个站点。 有关详细信息，请参阅 [SLES-SAP HANA 系统复制横向扩展性能优化方案](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites)中的“重要先决条件”部分。     

## <a name="set-up-the-infrastructure"></a>设置基础结构

在接下来的说明中，假设已创建资源组，即其中包含三个 Azure 网络子网的 Azure 虚拟网络：`client`、`inter` 和 `hsr`。

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机
1. 部署 Azure VM。  
对于本文档中介绍的配置，请部署七个虚拟机： 
   - 三个虚拟机充当 HANA 复制站点 1 的 HANA DB 节点：hana-s1-db1、hana-s1-db2 和 hana-s1-db3    
   - 三个虚拟机充当 HANA 复制站点 2 的 HANA DB 节点：hana-s2-db1、hana-s2-db2 和 hana-s2-db3    
   - 一个小型虚拟机充当多数仲裁节点：hana-s-mm

   作为 SAP DB HANA 节点部署的 VM 应由 SAP for HANA 认证，如 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中发布的一样。 部署 HANA DB 节点时，请确保已选择[加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md)。  
  
   对于多数仲裁节点，可以部署一个小型 VM，因为此 VM 不会运行任何 SAP HANA 资源。 在群集配置中使用多数仲裁 VM 可在拆分式方案中实现奇数个群集节点。 在此示例中，多数仲裁 VM 只需要 `client` 子网中的一个虚拟网络接口。        

   部署 `/hana/data` 和 `/hana/log` 的本地托管磁盘。 [SAP HANA Azure VM 存储配置](./hana-vm-operations-storage.md)中介绍了 `/hana/data` 和 `/hana/log` 的最小建议存储配置。

   为 `client` 虚拟网络子网中的每个 VM 部署主要网络接口。  
   通过 Azure 门户部署 VM 时，系统会自动生成网络接口名称。 在这些说明中，为了简单起见，我们将附加到 `client` Azure 虚拟网络子网的自动生成的主要网络接口称为“hana-s1-db1-client”、“hana-s1-db2-client”、“hana-s1-db3-client”等。    


   > [!IMPORTANT]
   > 请确保选择的操作系统已经过 SAP 针对使用的特定 VM 类型上的 SAP HANA 进行的认证。 有关经 SAP HANA 认证的 VM 类型和这些类型的 OS 版本列表，请访问[经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)网站。 单击进入列出的 VM 类型的详细信息，获取该类型的 SAP HANA 支持的操作系统版本的完整列表。  
  

2. 创建六个网络接口，`inter` 虚拟网络子网中的每个 HANA DB 虚拟机各一个（在此例中为“hana-s1-db1-inter”、“hana-s1-db2-inter、hana-s1-db3-inter”、“hana-s2-db1-inter”、“hana-s2-db2-inter”和“hana-s2-db3-inter”）。       

3. 创建六个网络接口，`hsr` 虚拟网络子网中的每个 HANA DB 虚拟机各一个（在此例中为“hana-s1-db1-hsr”、“hana-s1-db2-hsr”、“hana-s1-db3-hsr”、“hana-s2-db1-hsr”、“hana-s2-db2-hsr”和“hana-s2-db3-hsr”）。       

4. 将新创建的虚拟网络接口附加到相应的虚拟机：  

    a. 转到 [Azure 门户](https://portal.azure.com/#home)中的虚拟机。  

    b. 在左侧窗格中，选择“虚拟机”。 按虚拟机名称筛选（例如，“hana-s1-db1”），然后选择虚拟机。  

    c. 在“概述”窗格中，选择“停止”以解除分配虚拟机。  

    d. 选择“网络”，然后附加网络接口。 在“附加网络接口”下拉列表中，选择已为 `inter` 和 `hsr` 子网创建的网络接口。  
    
    e. 选择“保存”。  
 
    f. 对于剩余的虚拟机（在我们的示例中为 hana-s1-db2、hana-s1-db3、hana-s2-db1、hana-s2-db2 和 hana-s2-db3），请重复步骤 b 到 e    。
 
    g. 使虚拟机暂时处于停止状态。 接下来，我们将为所有新附加的网络接口启用[加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md)。  

5. 通过执行以下步骤，为 `inter` 和 `hsr` 子网的其他网络接口启用加速网络：  

    a. 在 [Azure 门户](https://portal.azure.com/#home)中打开 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。  

    b. 执行以下命令，为连接到 `inter` 和 `hsr` 子网的其他网络接口启用加速网络。  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. 启动 HANA DB 虚拟机

### <a name="deploy-azure-load-balancer"></a>部署 Azure 负载均衡器

1. 建议使用标准负载均衡器。 按照以下配置步骤来部署标准负载均衡器：
   1. 首先创建前端 IP 池：

      1. 打开负载均衡器，选择 **前端 IP 池**，然后选择“添加”。
      1. 输入新前端 IP 池的名称（例如 **hana-frontend**）。
      1. 将“分配”设置为“静态”并输入 IP 地址（例如，“10.23.0.27”）  。
      1. 选择“确定”。
      1. 创建新前端 IP 池后，请记下池 IP 地址。

   1. 接下来，创建一个后端池，并将所有群集 VM 添加到后端池：

      1. 打开负载均衡器，选择 **后端池**，然后选择“添加”。
      1. 输入新后端池的名称（例如 **hana-backend**）。
      1. 选择“添加虚拟机”。
      1. 选择“虚拟机”。
      1. 为 `client` 子网选择 SAP HANA 群集的虚拟机及其 IP 地址。
      1. 选择 **添加**。

   1. 接下来创建运行状况探测：

      1. 打开负载均衡器，选择 **运行状况探测**，然后选择“添加”。
      1. 输入新运行状况探测的名称（例如 **hana-hp**）。
      1. 选择“TCP”作为协议，并选择端口 625 **03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
      1. 选择“确定”。

   1. 接下来，创建负载均衡规则：
   
      1. 打开负载均衡器，选择 **负载均衡规则**，然后选择“添加”。
      1. 输入新负载均衡器规则的名称（例如 hana-lb）。
      1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend、hana-backend 和 hana-hp）。
      1. 选择“HA 端口”。
      1. 确保 **启用浮动 IP**。
      1. 选择“确定”。

   > [!IMPORTANT]
   > 负载均衡方案中的 NIC 辅助 IP 配置不支持浮动 IP。 有关详细信息，请参阅 [Azure 负载均衡器限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果你需要为 VM 提供其他 IP 地址，请部署第二个 NIC。    
   
   > [!Note]
   > 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)。  


   > [!IMPORTANT]
   > 请勿在放置于 Azure 负载均衡器之后的 Azure VM 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数“net.ipv4.tcp_timestamps”设置为“0”。 有关详细信息，请参阅[负载均衡器运行状况探测](../../../load-balancer/load-balancer-custom-probe-overview.md)。
   > 另请参阅 SAP 说明 [2382421](https://launchpad.support.sap.com/#/notes/2382421)。  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>部署 Azure NetApp 文件基础结构 

为 `/hana/shared` 文件系统部署 ANF 卷。 对于每个 HANA 系统复制站点，都需要一个单独的 `/hana/shared` 卷。 有关详细信息，请参阅[设置 Azure NetApp 文件基础结构](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure)。

在此例中，使用了以下 Azure NetApp 文件卷： 

* 卷 HN1-shared-s1 (nfs://10.23.1.7/HN1-shared-s1) 
* 卷 HN1-shared-s2 (nfs://10.23.1.7/HN1-shared-s2) 


## <a name="operating-system-configuration-and-preparation"></a>操作系统配置和准备

后续各节中的说明以下列缩写之一作为前缀：
* **[A]** ：适用于所有节点
* **[AH]** ：适用于所有 HANA DB 节点
* **[M]** ：适用于多数仲裁节点
* **[AH1]** ：适用于站点 1 上的所有 HANA DB 节点
* **[AH2]** ：适用于站点 2 上的所有 HANA DB 节点
* **[1]** ：仅适用于 HANA DB 节点 1，站点 1
* **[2]** ：仅适用于 HANA DB 节点 1，站点 2

通过执行以下步骤来配置和准备 OS：

1. **[A]** 维护虚拟机上的主机文件。 包括所有子网的条目。 在此示例中，向 `/etc/hosts` 添加了以下条目。  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

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

4. **[A]** 根据 [NetApp SAP Applications on Microsoft Azure using Azure NetApp Files][anf-sap-applications-azure]（Microsoft Azure 上使用 Azure NetApp 文件的 Netapp SAP 应用程序）中的建议调整 sunrpc 设置。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

2. **[A]** SUSE 为 SAP HANA 提供特殊的资源代理，默认情况下，安装了 SAP HANA ScaleUp 的代理。 卸载 ScaleUp 的包（如果已安装），并安装方案 SAP HANAScaleOut 的包。 需要在所有群集 VM（包括多数仲裁虚拟机）上执行此步骤。   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[AH]** 准备 VM - 按照 SAP 说明 [2205917] 为 SUSE Linux Enterprise Server for SAP Application 应用推荐的设置。  

## <a name="prepare-the-file-systems"></a>准备文件系统
### <a name="mount-the-shared-file-systems"></a>装载共享文件系统

在此示例中，共享 HANA 文件系统部署在 Azure NetApp 文件上，并通过 NFSv4 装载。  

1. **[AH]** 为 HANA 数据库卷创建装入点。  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[AH]** 验证 NFS 域设置。 请确保域配置为默认的 Azure NetApp 文件域，即 `defaultv4iddomain.com`，并且映射设置为“无人” 。  
   仅当使用 Azure NetAppFiles NFSv4.1 时，才需要执行此步骤。  

    > [!IMPORTANT]
    > 确保在 VM 上的 `/etc/idmapd.conf` 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置：“`defaultv4iddomain.com`”。 如果 NFS 客户端（即 VM）上的域配置和 NFS 服务器（即 Azure NetApp 配置）之间不匹配，则 VM 上已装载的 Azure NetApp 卷上文件的权限将显示为 `nobody`。  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** 验证 `nfs4_disable_idmapping`。 它应设置为“Y”。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。  
   仅当使用 Azure NetAppFiles NFSv4.1 时，才需要执行此步骤。  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** 在 SITE1 HANA DB VM 上装载共享的 Azure NetApp 文件卷。  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** 在 SITE2 HANA DB VM 上装载共享的 Azure NetApp 文件卷。  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[AH]** 验证是否在使用 NFS 协议版本 NFSv4 的所有 HANA DB VM 上装载了相应的 `/hana/shared/`文件系统。  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>准备数据并记录本地文件系统
在所提供的配置中，文件系统 `/hana/data` 和 `/hana/log` 部署在托管磁盘上，并本地附加到每个 HANA DB VM。 需要执行以下步骤，才能在每个 HANA DB 虚拟机上创建本地数据和日志卷。 

使用“逻辑卷管理器 (LVM)”设置磁盘布局。 以下示例假设每个 HANA 虚拟机上附加了三个用于创建两个卷的数据磁盘。

1. **[AH]** 列出所有可用的磁盘：
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   示例输出：

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** 为想要使用的所有磁盘创建物理卷：
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** 为数据文件创建卷组。 将一个卷组用于日志文件，将另一个卷组用于 SAP HANA 的共享目录：
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** 创建逻辑卷。 
   线性卷是使用不带 `-i` 开关的 `lvcreate` 创建的。 我们建议你创建一个带区卷，以获得更好的 I/O 性能，并将带区大小与 [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)中记录的值保持一致。 `-i` 参数应表示基础物理卷的数量，`-I` 参数则表示带区大小。 在本文档中，两个物理卷用于数据卷，因此 `-i` 开关参数设置为 **2**。 数据卷的带区大小为 256 KiB。 一个物理卷用于日志卷，因此，`-i` 或 `-I` 开关不会显式用于日志卷命令。  

   > [!IMPORTANT]
   > 对每个数据或日志卷使用多个物理卷时，请使用 `-i` 开关，并将其设置为基础物理卷的数量。 创建带区卷时，请使用 `-I` 开关来指定带区大小。  
   > 有关建议的存储配置，包括带区大小和磁盘数量，请参阅 [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)。  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** 创建装载目录，并复制所有逻辑卷的 UUID：
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** 为逻辑卷创建 `fstab` 条目并进行装载：
    ```bash
    sudo vi /etc/fstab
    ```

   将以下行插入 `/etc/fstab` 文件：

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   装载新卷：

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的步骤为此 HANA 服务器创建一个基本 Pacemaker 群集。
包括所有虚拟机，其中包括群集中的多数仲裁虚拟机。  

> [!IMPORTANT]
> 请勿将 `quorum expected-votes` 设置为 2，因为这不是一个双节点群集。  
> 确保已启用群集属性 `concurrent-fencing`，以便反序列化节点隔离。   

## <a name="installation"></a>安装  

在此示例中，使用 Azure VM 上的 HSR 以横向扩展配置部署 SAP HANA 时，我们使用了 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>准备 HANA 安装

1. **[AH]** 安装 HANA 之前，请设置根密码。 可以在安装完成后禁用根密码。 以 `root` 身份执行命令 `passwd`。  

2. **[1,2]** 更改对 `/hana/shared` 的权限 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** 验证是否可以通过 SSH 登录到站点 hana-s1-db2 和 hana-s1-db3 中的 HANA DB VM，而不会提示输入密码 。 如果不是这种情况，请按[通过公钥启用 SSH 访问](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional)中所述的方式交换 ssh 密钥。  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** 验证是否可以通过 SSH 登录到站点 hana-s2-db2 和 hana-s2-db3 中的 HANA DB VM，而不会提示输入密码 。  
   如果不是这种情况，请交换 ssh 密钥。    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** 安装 HANA 2.0 SP4 所需的其他包。 有关详细信息，请参阅适用于 SLES 版本的 SAP 说明 [2593824](https://launchpad.support.sap.com/#/notes/2593824)。 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>每个站点上第一个节点上的 HANA 安装

1. **[1]** 按照 [SAP HANA 2.0 安装和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的说明安装 SAP HANA。 在接下来的说明中，将介绍如何在站点 1 的第一个节点上安装 SAP HANA。   

   a. 以 `root` 身份从 HANA 安装软件目录启动 hdblcm 程序。 使用 `internal_network` 参数并传递子网的地址空间，用于内部 HANA 节点间通信。  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. 在提示符处输入以下值：

     * 对于“选择操作”：输入 1（用于安装）
     * 对于“要安装的其他组件”：输入 2、3 
     * 对于安装路径：按 Enter（默认为 /hana/shared）
     * 对于“本地主机名”：按 Enter 接受默认值
     * 对于“是否要将主机添加到系统？”：输入 n
     * 对于“SAP HANA 系统 ID”：输入 HN1
     * 对于“实例号”[00]：输入 03
     * 对于“本地主机辅助角色组”[默认值]：按 Enter 接受默认值
     * 对于“选择系统使用情况/输入索引[4]”：输入 4（用于自定义）
     * 对于“数据卷的位置”[/hana/data/HN1]：按 Enter 接受默认值
     * 对于“日志卷的位置”[/hana/log/HN1]：按 Enter 接受默认值
     * 对于“是否限制最大内存分配？” [n]：输入 n
     * 对于“主机 hana-s1-db1 的证书主机名”[hana-s1-db1]：按 Enter 接受默认值
     * 对于“SAP 主机代理用户 (sapadm) 密码”：输入密码
     * 对于“确认 SAP 主机代理用户 (sapadm) 密码”：输入密码
     * 对于“系统管理员 (hn1adm) 密码”：输入密码
     * 对于“系统管理员主目录”[/usr/sap/HN1/home]：按 Enter 接受默认值
     * 对于“系统管理员登录 Shell”[/bin/sh]：按 Enter 接受默认值
     * 对于“系统管理员用户 ID”[1001]：按 Enter 接受默认值
     * 对于“用户组的输入 ID (sapsys)”[79]：按 Enter 接受默认值
     * 对于“系统数据库用户 (system) 密码”：输入系统的密码
     * 对于“确认系统数据库用户(system)密码”：输入系统的密码
     * 对于“重新引导计算机后是否重启系统?” [n]：输入 n 
     * 对于“是否要继续 (y/n)”：验证摘要，如果一切正常，请输入 y

2. **[2]** 重复上一步，在站点 2 上的第一个节点上安装 SAP HANA。   

3. **[1,2]** 验证 global.ini  

   显示 global.ini，并确保 SAP HANA 内部节点间通信的配置已就位。 验证 communication 节。 它应该具有 `inter` 子网的地址空间，并且 `listeninterface` 应设置为 `.internal`。 验证 internal_hostname_resolution 节。 它应该具有属于 `inter` 子网的 HANA 虚拟机的 IP 地址。  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1,2]** 准备好 `global.ini` 以在非共享环境中安装，如 SAP 说明 [2080991](https://launchpad.support.sap.com/#/notes/0002080991) 中所述。  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1,2]** 重启 SAP HANA 以激活更改。  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1,2]** 验证客户端接口是否将使用 `client` 子网中的 IP 地址进行通信。  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   有关如何验证配置的信息，请参阅 SAP 说明 [2183363 - SAP HANA 内部网络配置](https://launchpad.support.sap.com/#/notes/2183363)。  

7. **[AH]** 更改对数据和日志目录的权限，以避免 HANA 安装错误。  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** 安装辅助 HANA 节点。 此步骤中的示例说明适用于站点 1。  
   a. 以 `root` 身份启动常驻 hdblcm 程序。    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. 在提示符处输入以下值：

     * 对于“选择操作”：输入 2（用于添加主机）
     * 对于“输入要添加的逗号分隔的主机名”：hana-s1-db2、hana-s1-db3
     * 对于“要安装的其他组件”：输入 2、3 
     * 对于“输入根用户名 [root]”：按 Enter 接受默认值
     * 对于“选择主机 'hana-s1-db2' 的角色 [1]”：1（适用于辅助角色）
     * 对于“输入主机 'hana-s1-db2' 的主机故障转移组 [默认值]”：按 Enter 接受默认值
     * 对于“输入主机‘hana-s1-db2’的存储分区号 [\<\<assign automatically\>\>]”：按 Enter 接受默认值
     * 对于“输入主机 'hana-s1-db2' 的辅助角色组 [默认值]”：按 Enter 接受默认值
     * 对于“选择主机 'hana-s1-db3' 的角色 [1]”：1（适用于辅助角色）
     * 对于“输入主机 'hana-s1-db3' 的主机故障转移组 [默认值]”：按 Enter 接受默认值
     * 对于“输入主机‘hana-s1-db3’的存储分区号 [\<\<assign automatically\>\>]”：按 Enter 接受默认值
     * 对于“输入主机 'hana-s1-db3' 的辅助角色组 [默认值]”：按 Enter 接受默认值
     * 对于“系统管理员 (hn1adm) 密码”：输入密码
     * 对于“输入 SAP 主机代理用户 (sapadm) 密码”：输入密码
     * 对于“确认 SAP 主机代理用户 (sapadm) 密码”：输入密码
     * 对于“主机 hana-s1-db2 的证书主机名”[hana-s1-db2]：按 Enter 接受默认值
     * 对于“主机 hana-s1-db3 的证书主机名”[hana-s1-db3]：按 Enter 接受默认值
     * 对于“是否要继续 (y/n)”：验证摘要，如果一切正常，请输入 y

9. **[2]** 重复上一步以在站点 2 上安装辅助 SAP HANA 节点。   

## <a name="configure-sap-hana-20-system-replication"></a>配置 SAP HANA 2.0 系统复制

1. **[1]** 在站点 1 上配置系统复制：

   以 hn1adm 身份备份数据库：

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   将系统 PKI 文件复制到辅助站点：

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   创建主站点：

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** 在站点 2 上配置系统复制：
    
   注册第二个站点以启动系统复制。 以 <hanasid\>adm 身份运行以下命令：

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** 检查复制状态

   检查复制状态并等待，直到所有数据库都保持同步。

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1,2]** 更改 HANA 配置，以便 HANA 系统复制通信通过 HANA 系统复制虚拟网络接口进行。   
   - 在两个站点上停止 HANA
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - 编辑 global.ini 以便为 HANA 系统复制添加主机映射：使用 `hsr` 子网中的 IP 地址。  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - 在两个站点上启动 HANA
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   有关详细信息，请参阅[系统复制的主机名解析](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)。  

## <a name="create-file-system-resources"></a>创建文件系统资源

创建一个虚拟文件系统群集资源，它将监视和报告故障，以防在访问 NFS 装载的文件系统 `/hana/shared` 时出现问题。 此操作能够在访问 `/hana/shared` 出现问题时，使群集触发故障转移。 有关详细信息，请参阅[为 HANA 系统复制处理 SUSE HA 群集中失败的 NFS 共享](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** 将 pacemaker 置于维护模式，以便为创建 HANA 群集资源做准备。  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1,2]** 在 ANF /hana/sahred 卷上创建目录，该目录将用于特殊的文件系统监视资源。 需要在两个站点上创建目录。  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[AH]** 创建将用于装载特殊文件系统监视资源的目录。 需要在所有 HANA 群集节点上创建目录。  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** 创建文件系统群集资源。     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   在监视操作中添加 `OCF_CHECK_LEVEL=20` 属性，以便监视操作在文件系统上执行读取/写入测试。 如果没有此属性，则监视操作仅验证是否已装载文件系统。 这可能是一个问题，因为连接丢失时，尽管无法访问文件系统，但它仍可能保持装载状态。  

   `on-fail=fence` 属性也会添加到监视操作中。 使用此选项时，如果监视操作在某个节点上失败，则会立即隔离该节点。   

## <a name="create-sap-hana-cluster-resources"></a>创建 SAP HANA 群集资源

1. **[1,2]** 安装 HANA“系统复制挂钩”。 需要在每个系统复制站点上的一个 HANA DB 节点上安装该挂钩。         

   1. 以 `root` 身份准备挂钩 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 停止两个系统复制站点上的 HANA。 以 <sid\>adm 身份执行：
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. 调整 `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[AH]** 群集需要在群集节点上为 <sid\>adm 配置 sudoers。 在此示例中，通过创建新文件来实现此目的。 以 `root` 身份执行命令。    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1,2]** 在两个复制站点上启动 SAP HANA。 以 <sid\>adm 身份执行。  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** 验证是否安装了挂钩。 在活动 HANA 系统复制站点上以 <sid\>adm 的身份执行。   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** 创建 HANA 群集资源。 以 `root` 身份执行以下命令。    
   1. 请确保群集已处于维护模式。  
    
   2. 接下来，创建 HANA 拓扑资源。  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. 接下来，创建 HANA 实例资源。  
      > [!NOTE] 
      > 本文包含对术语“主”和“从”的引用，Microsoft 不再使用这些术语 。 从软件中删除这些术语后，我们会将其从本文中删除。
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > 建议的最佳做法是，仅将 AUTOMATED_REGISTER 设置为“否”，同时执行全面的故障转移测试，以防止失败的主实例自动注册为辅助实例。 故障转移测试成功完成后，将 AUTOMATED_REGISTER 设置为“是”，以便在接管后系统复制自动继续。 

   4. 创建虚拟 IP 和关联的资源。  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. 创建群集约束  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** 配置其他群集属性   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** 验证挂钩与群集之间的通信
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** 将群集退出维护模式。 请确保群集状态正常，并且所有资源都已启动。  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > 上述配置中的超时只是示例，可能需要根据特定的 HANA 设置进行调整。 例如，如果启动 SAP HANA 数据库需要较长时间，则可能需要增加启动超时。
  

## <a name="test-sap-hana-failover"></a>测试 SAP HANA 故障转移 

> [!NOTE]
> 本文包含对术语“主”和“从”的引用，Microsoft 不再使用这些术语 。 从软件中删除这些术语后，我们会将其从本文中删除。

1. 在开始测试前，请检查群集和 SAP HANA 系统复制状态。    

   a. 验证是否不存在失败的群集操作  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. 验证 SAP HANA 系统复制是否同步

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. 建议通过执行 [SLES 上 Azure VM 上的 SAP HANA 的 HA](./sap-hana-high-availability.md#test-the-cluster-setup) 和 [SLES 复制横向扩展性能优化方案](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster)中所述的测试，全面验证 SAP HANA 群集配置。

3. 验证节点无法访问 NFS 共享 (`/hana/shared`) 的故障情况下的群集配置。  

   SAP HANA 资源代理依赖于二进制文件，这些文件存储在 `/hana/shared` 中，以便在故障转移过程中执行操作。 文件系统 `/hana/shared` 以所提供的配置通过 NFS 装载。 可以执行的测试是创建一个临时防火墙规则，用于阻止在某个主站点 VM 上对 `/hana/shared` ANF 卷的访问。 此方法验证如果在活动系统复制站点上丢失对 `/hana/shared` 的访问权限，群集是否会进行故障转移。  

   预期结果：在某个主站点 VM 上阻止访问 `/hana/shared` ANF 卷时，对文件系统执行读/写操作的监视操作将会失败，因为它无法访问文件系统并将触发 HANA 资源故障转移。 当 HANA 节点失去对 NFS 共享的访问权限时，会出现相同的结果。  
     
   可以通过执行 `crm_mon` 或 `crm status` 来检查群集资源的状态。 开始测试之前的资源状态：
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   若要模拟 `/hana/shared` 故障，请先在主站点上确认 `/hana/shared` ANF 卷的 IP 地址。 可以通过运行 `df -kh|grep /hana/shared` 来实现此目的。 
   然后，设置一个临时防火墙规则来阻止对 `/hana/shared` ANF 卷的 IP 地址的访问，方法是在其中一个主 HANA 系统复制站点 VM 上执行以下命令。
   在此示例中，命令是在 hana-s1-db1 上执行的。

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   群集资源将迁移到其他 HANA 系统复制站点。    
              
   如果将 AUTOMATED_REGISTER 设置为“false”，则需要在辅助站点上配置 SAP HANA 系统复制。 在这种情况下，可以执行以下命令来将 SAP HANA 重新配置为辅助站点。   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   测试后的资源状态为： 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)
* 若要了解如何在 Azure VM 上建立 SAP HANA 的高可用性和灾难恢复计划，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 的高可用性][sap-hana-ha]。