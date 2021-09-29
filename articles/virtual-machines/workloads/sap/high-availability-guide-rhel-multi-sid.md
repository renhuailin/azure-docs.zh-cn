---
title: RHEL 多 SID 上 SAP NW 的 Azure VM 高可用性指南 | Microsoft Docs
description: 在 Azure 虚拟机 (VM) RHEL 多 SID 上建立 SAP NW 的高可用性。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/03/2021
ms.author: radeltch
ms.openlocfilehash: efbb1370ace9936f4ac7e42bc6c8feac55525002
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774056"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>适用于 SAP 应用程序多 SID 的 Red Hat Enterprise Linux 的 Azure VM 上的 SAP NetWeaver 高可用性指南

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:../../../azure-netapp-files/azure-netapp-files-introduction.md
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

本文介绍如何在使用 Red Hat Enterprise Linux for SAP Applications 的 Azure VM 上，在双节点群集中部署多个 SAP NetWeaver 高度可用的系统（即多 SID）。  

在示例配置、安装命令等中，在单个双节点高可用性群集中部署了三个 SAP NetWeaver 7.50 系统。 SAP 系统 SID 包括：
* **NW1**：ASCS 实例号 00 和虚拟主机名 msnw1ascs；ERS 实例号 02 和虚拟主机名 msnw1ers   。  
* **NW2**：ASCS 实例号 10 和虚拟主机名 msnw2ascs；ERS 实例号 12 和虚拟主机名 msnw2ers   。  
* **NW3**：ASCS 实例号 20 和虚拟主机名 msnw3ascs；ERS 实例号 22 和虚拟主机名 msnw3ers   。  

本文不涉及 SAP NFS 共享的数据库层和部署。 在本文的示例中，我们将 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) 卷 sapMSID 用于 NFS 共享，前提是该卷已部署。 我们也假设，Azure NetApp 文件卷是用 NFSv3 协议部署的，并且 SAP 系统 NW1、NW2 和 NW3 的 ASCS 和 ERS 实例的群集资源存在以下文件路径中：  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

在开始之前，请先参阅以下 SAP 说明和文章：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* [Azure NetApp 文件文档][anf-azure-doc]
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2002167] 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP Netweaver in pacemaker cluster](https://access.redhat.com/articles/3150081)（Pacemaker 群集中的 SAP NetWeaver）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuring ASCS/ERS for SAP Netweaver with standalone resources in RHEL 7.5](https://access.redhat.com/articles/3569681)（使用 RHEL 7.5 中的独立资源为 SAP NetWeaver 配置 ASCS/ERS）
  * [在 RHEL 上的 Pacemaker 中使用 Standalone Enqueue Server 2 (ENSA2) 配置 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
* [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

如果发生故障转移，则必须调整加入群集的虚拟机的大小，使其能够运行所有资源。 在多 SID 高可用性群集中，每个 SAP SID 都可以相互独立地进行故障转移。  

为了实现高可用性，SAP NetWeaver 需要高度可用的共享。 在本文档中，我们介绍了部署在 [Azure NetApp 文件 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)上的 SAP 共享的示例。 还可以在高度可用的 [GlusterFS 群集](./high-availability-guide-rhel-glusterfs.md)上托管共享，这些共享可以由多个 SAP 系统使用。  

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> 对于 Azure VM 中将 Red Hat Linux 作为来宾操作系统的 SAP ASCS/ERS 的多 SID 群集的支持仅限于在同一群集上提供五个 SAP SID。 每个新 SID 都增加了复杂性。 不支持在同一个群集中混合使用 SAP 排队复制服务器 1 和排队复制服务器 2。 多 SID 群集介绍了如何在一个 Pacemaker 群集中安装多个具有不同 SID 的 SAP ASCS/ERS 实例。 目前仅 ASCS/ERS 支持多 SID 群集。  

> [!TIP]
> SAP ASCS/ERS 的多 SID 群集是更为复杂的解决方案。 它实现起来更为复杂。 执行 OS 修补等维护活动时，它还涉及更多的管理工作。 开始实际执行之前，请花些时间仔细规划部署和所有涉及的组件，如 VM、NFS 装载、VIP、负载平衡器配置等。  

SAP NetWeaver ASCS、SAP NetWeaver SCS 和 SAP NetWeaver ERS 使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 建议使用[标准负载均衡器](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)。  

以下列表显示了这个具有三个 SAP 系统的多 SID 群集示例的 (A)SCS 和 ERS 负载均衡器的配置。 每个 ASCS 和 ERS 实例的每个 SID 都需要单独的前端 IP、运行状况探测和负载均衡规则。 将属于 ASCS/ASCS 群集的所有 VM 分配到一个单个 ILB 的后端池。  

### <a name="ascs"></a>(A)SCS

* 前端配置
  * NW1 的 IP 地址：10.3.1.50
  * NW2 的 IP 地址：10.3.1.52
  * NW3 的 IP 地址：10.3.1.54

* 探测端口
  * 端口 620&lt;nr&gt;，因此对于 NW1、NW2 和 NW3，探测端口分别为 62000、62010 和 62020  
* 负载均衡规则 - 为每个实例（即 NW1/ASCS、NW2/ASCS 和 NW3/ASCS）创建一个负载均衡规则。
  * 如果使用“标准负载均衡器”，请选择“HA 端口”
  * 如果使用“基本负载均衡器”，请为以下端口创建负载均衡规则
    * 32&lt;nr&gt; TCP
    * 36&lt;nr&gt; TCP
    * 39&lt;nr&gt; TCP
    * 81&lt;nr&gt; TCP
    * 5&lt;nr&gt;13 TCP
    * 5&lt;nr&gt;14 TCP
    * 5&lt;nr&gt;16 TCP

### <a name="ers"></a>ERS

* 前端配置
  * NW1 的 IP 地址：10.3.1.51
  * NW2 的 IP 地址：10.3.1.53
  * NW3 的 IP 地址：10.3.1.55

* 探测端口
  * 端口 621&lt;nr&gt;，因此对于 NW1、NW2 和 N3，探测端口分别为 62102、62112 和 62122  
* 负载均衡规则 - 为每个实例（即 NW1/ERS、NW2/ERS 和 NW3/ERS）创建一个负载均衡规则。
  * 如果使用“标准负载均衡器”，请选择“HA 端口”
  * 如果使用“基本负载均衡器”，请为以下端口创建负载均衡规则
    * 32&lt;nr&gt; TCP
    * 33&lt;nr&gt; TCP
    * 5&lt;nr&gt;13 TCP
    * 5&lt;nr&gt;14 TCP
    * 5&lt;nr&gt;16 TCP

* 后端配置
  * 连接到所有虚拟机（这些虚拟机应为 (A)SCS/ERS 群集的一部分）的主网络接口

> [!IMPORTANT]
> 负载均衡方案中的 NIC 辅助 IP 配置不支持浮动 IP。 有关详细信息，请参阅 [Azure 负载均衡器限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果你需要为 VM 提供其他 IP 地址，请部署第二个 NIC。  

> [!Note]
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)。  

> [!IMPORTANT]
> 请勿在放置于 Azure 负载均衡器之后的 Azure VM 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数“net.ipv4.tcp_timestamps”设置为“0”。 有关详细信息，请参阅[负载均衡器运行状况探测](../../../load-balancer/load-balancer-custom-probe-overview.md)。

## <a name="sap-shares"></a>SAP 共享

SAP NetWeaver 要求对传输和配置文件目录等使用共享的存储。 对于高度可用的 SAP 系统，具有高度可用的共享非常重要。 需要确定 SAP 共享的体系结构。 另一个选项是在 [Azure NetApp 文件 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)上部署共享。  使用 Azure NetApp 文件时，将获得 SAP NFS 共享的内置高可用性。

另一个选择是在 [Red Hat Enterprise Linux for SAP NetWeaver 上的 Azure VM 构建 GlusterFS](./high-availability-guide-rhel-glusterfs.md)，这可以在多个 SAP 系统之间共享。 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>在群集中部署首个 SAP 系统

现在确定了 SAP 共享的体系结构，请按照相应的文档在群集中部署第一个 SAP 系统。

* 如果使用 Azure NetApp 文件 NFS 卷，请遵循[具有 Azure NetApp Files for SAP Applications 的 Red Hat Enterprise Linux 上 SAP NetWeaver 的 Azure VM 高可用性](./high-availability-guide-rhel-netapp-files.md)  
* 如果使用 GlusterFS 群集，请遵循[适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS](./high-availability-guide-rhel-glusterfs.md)。  

以上列出的文档将指导你完成准备所需基础结构、构建群集、为运行 SAP 应用程序准备 OS 的步骤。  

> [!TIP]
> 部署第一个系统后，请始终测试该群集的故障转移功能，再将其他 SAP SID 添加到该群集。 这样，在将其他 SAP 系统的复杂性添加到群集之前，就可以了解群集功能的运行状况。   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>在群集中部署其他 SAP 系统

在此示例中，我们假定已在群集中部署了系统 NW1。 我们将演示如何在群集 SAP 系统 NW2 和 NW3 中进行部署 。 

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2  。

### <a name="prerequisites"></a>先决条件 

> [!IMPORTANT]
> 在按照说明在群集中部署其他 SAP 系统之前，请按照说明在群集中部署第一个 SAP 系统，因为有些步骤只需在部署第一个系统中执行。  

本文假设：
* 已配置 Pacemaker 群集，且它正在运行。  
* 已部署至少一个 SAP 系统（ASCS/ERS 实例），且它正在群集中运行。  
* 已测试群集故障转移功能。  
* 所有 SAP 系统的 NFS 共享均已部署。  

### <a name="prepare-for-sap-netweaver-installation"></a>准备 SAP Netweaver 安装

1. 按照说明[通过 Azure 门户手动部署 Azure 负载均衡器](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal)，将新部署的系统（即 NW2、NW3）的配置添加到现有 Azure 负载均衡器 。 调整配置的 IP 地址、运行状况探测端口和负载均衡规则。  

2. **[A]** 设置其他 SAP 系统的名称解析。 可以使用 DNS 服务器，或修改所有节点上的 `/etc/hosts`。 此示例演示如何使用 `/etc/hosts` 文件。  根据环境调整 IP 地址和主机名。 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** 为要部署到群集的其他 NW2 和 NW3 SAP 系统创建共享目录 。 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** 为要部署到群集的其他 SAP 系统添加 /sapmnt/SID 和 /usr/sap/SID/SYS 文件系统的其装载条目。 在此示例中为 NW2 和 NW3 。  

   使用要部署到群集的其他 SAP 系统的文件系统来更新文件 `/etc/fstab`。  

   * 如果使用 Azure NetApp 文件 NFS 卷，请按照[具有 Azure NetApp 文件的 RHEL 上 SAP NW 的 Azure VM 高可用性](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)页中的说明执行操作
   * 如果使用 GlusterFS 群集，请按照 [RHEL 上 SAP NW 的 Azure VM 高可用性](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)页中的说明执行操作

### <a name="install-ascs--ers"></a>安装 ASCS/ERS

1. 为要部署到群集的其他 SAP 系统的 ASCS 实例创建虚拟 IP 和运行状况探测群集资源。 此处所示的示例针对的是 NW2 和 NW3 ASCS，在 Azure NetApp 文件卷上使用带有 NFSv3 协议的 NFS。   

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
  
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。  

2. [1] 安装 SAP NetWeaver ASCS  

   使用映射到 ASCS 负载均衡器前端配置的 IP 地址的虚拟主机名以 root 身份安装 SAP NetWeaver ASCS。 例如，对于系统 NW2，虚拟主机名为 msnw2ascs, 10.3.1.52，而用于负载均衡器探测的实例号为 10<b></b><b></b><b></b>。 对于系统 NW3，虚拟主机名为 msnw3ascs, 10.3.1.54，而用于负载均衡器探测的实例号为 20<b></b><b></b><b></b>。 记下在哪个群集节点上安装了每个 SAP SID 的 ASCS。  

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。 可以使用参数 SAPINST_USE_HOSTNAME，以通过使用虚拟主机名来安装 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   如果安装过程无法在 /usr/sap/SID/ASCSInstance# 中创建子文件夹，请尝试将所有者设置为 sidadm 并将组设置为 ASCSInstance# 的 sapsys，然后重试   。

3. **[1]** 为要部署到群集的其他 SAP 系统的 ERS 实例创建虚拟 IP 和运行状况探测群集资源。 此处所示的示例针对的是 NW2 和 NW3 ERS，在 Azure NetApp 文件卷上使用带有 NFSv3 协议的 NFS。   

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   请确保群集状态正常，并且所有资源都已启动。  

   接下来，请确保新创建的 ERS 组的资源在群集节点上运行，这与安装了同一 SAP 系统的 ASCS 实例的群集节点相反。  例如，如果在 `rhelmsscl1` 上安装了 NW2 ASCS，请确保 NW2 ERS 组在 `rhelmsscl2` 上运行。  要将 NW2 ERS 组迁移到 `rhelmsscl2`，可以为组中的其中一个群集资源运行以下命令： 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. [2] 安装 SAP Netweaver ERS

   使用映射到 ERS 负载均衡器前端配置的 IP 地址的虚拟主机名以 root 身份在其他节点上安装 SAP NetWeaver ERS。 例如，对于系统 NW2，虚拟主机名为 msnw2ers, 10.3.1.53，而用于负载均衡器探测的实例号为 12<b></b><b></b><b></b>。 对于系统 NW3，虚拟主机名为 msnw3ers, 10.3.1.55，而用于负载均衡器探测的实例号为 22<b></b><b></b><b></b>。 

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。 可以使用参数 SAPINST_USE_HOSTNAME，以通过使用虚拟主机名来安装 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > 使用 SWPM SP 20 PL 05 或更高版本。 较低版本不会正确设置权限，安装将失败。

   如果安装过程无法在 /usr/sap/NW2/ERSInstance# 中创建子文件夹，请尝试将所有者设置为 sidadm 并将组设置为 ERSInstance# 文件夹的 sapsys，然后重试   。

   如果需要将新部署的 SAP 系统的 ERS 组迁移到其他群集节点，请不要忘记删除 ERS 组的位置约束。 可以通过运行以下命令来删除约束（示例针对 SAP 系统 NW2 和 NW3） 。 请务必删除在命令中用于移动 ERS 群集组的同一资源的临时约束。

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** 为新安装的 SAP 系统调整 ASCS/SCS 和 ERS 实例配置文件。 下面显示的示例针对 NW2。 将需要为添加到群集的所有 SAP 实例调整 ASCS/SCS 和 ERS 配置文件。  
 
   * ASCS/SCS 配置文件

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     对于 ENSA1 和 ENSA2，请确保按 SAP 说明 [1410736](https://launchpad.support.sap.com/#/notes/1410736) 中所述设置 `keepalive` OS 参数。    

   * ERS 配置文件

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** 更新 /usr/sap/sapservices 文件

   要防止 sapinit 启动脚本启动实例，必须从 `/usr/sap/sapservices` 文件中注释掉由 Pacemaker 管理的所有实例。  下面显示的示例针对 SAP 系统 NW2 和 NW3 。  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** 为新安装的 SAP 系统创建 SAP 群集资源。  

   如果使用排队服务器 1 体系结构 (ENSA1)，请定义 SAP 系统 NW2 和 NW3 的资源，如下所示 ：

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource meta g-NW2_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource meta g-NW3_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   从 SAP NW 7.52 开始，SAP 引入了对排队服务器 2 的支持，包括复制。 从 ABAP 平台 1809 开始，系统将默认安装排队服务器 2。 有关排队服务器 2 的支持，请参阅 SAP 说明 [2630416](https://launchpad.support.sap.com/#/notes/2630416)。
   如果使用排队服务器 2 体系结构 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html))，请定义 SAP 系统 NW2 和 NW3 的资源，如下所示 ：

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource meta g-NW2_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs resource meta rsc_sap_NW2_ERS12  resource-stickiness=3000

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order start g-NW2_ASCS then start g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource meta g-NW3_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs resource meta rsc_sap_NW3_ERS22  resource-stickiness=3000

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order start g-NW3_ASCS then start g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   如果要从旧版本升级并切换到排队服务器 2，请参阅 SAP 说明 [2641019](https://launchpad.support.sap.com/#/notes/2641019)。 

   > [!NOTE]
   > 上述配置中的超时只是示例，可能需要根据特定的 SAP 设置进行调整。 

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。
   以下示例显示了将 SAP 系统 NW2 和 NW3 添加到群集之后群集资源状态 。 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** 在两个节点上为 ASCS 和 ERS 添加防火墙规则。  以下示例显示了同时适用于 SAP 系统 NW2 和 NW3 的防火墙规则。   

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>继续安装 SAP 

通过执行以下内容来完成 SAP 安装：

* [准备 SAP NetWeaver 应用程序服务器](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [安装 DBMS 实例](./high-availability-guide-rhel-netapp-files.md#install-database)
* [安装主 SAP 应用程序服务器](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* 安装一个或多个其他 SAP 应用程序实例

## <a name="test-the-multi-sid-cluster-setup"></a>测试多 SID 群集设置

以下测试属于 Red Hat 最佳做法指南中的测试用例。 为方便起见，已将其包括在内。 有关群集测试的完整列表，请参阅以下文档：

* 如果使用 Azure NetApp 文件 NFS 卷，请遵循[具有 Azure NetApp Files for SAP Applications 的 RHEL 上 SAP NetWeaver 的 Azure VM 高可用性](./high-availability-guide-rhel-netapp-files.md)
* 如果使用高可用 `GlusterFS`，请遵循 [RHEL for SAP Applications 上 SAP NetWeaver 的 Azure VM 高可用性](./high-availability-guide-rhel.md)。  

请务必阅读 Red Hat 最佳做法指南，并执行可能已经添加的所有其他测试。  
提供的测试位于安装了三个 SAP 系统的双节点多 SID 群集中。  

1. 手动迁移 ASCS 实例。 该示例演示了如何迁移 SAP 系统 NW3 的 ASCS 实例。

   开始测试之前的资源状态：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   以 root 身份运行以下命令来迁移 NW3 ASCS 实例。

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   测试之后的资源状态：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. 模拟节点故障

   开始测试之前的资源状态：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   以 root 身份在运行有至少一个 ASCS 实例的节点上运行以下命令。 在此示例中，我们在运行 NW1、NW2 和 NW3 的 ASCS 实例的 `rhelmsscl1` 上执行了命令。  

   ```
   echo c > /proc/sysrq-trigger
   ```

   崩溃后重新启动的测试和节点之后的状态应如下所示：

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   如果有针对失败资源的消息，请清除失败资源的状态。 例如：

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
