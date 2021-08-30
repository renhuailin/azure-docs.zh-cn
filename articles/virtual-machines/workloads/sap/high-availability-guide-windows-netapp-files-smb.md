---
title: 使用 Azure NetApp 文件 (SMB) 实现 Windows 上 Azure VM 的 SAP NW 高可用性 | Microsoft Docs
description: 使用适用于 SAP 应用程序的 Azure NetApp 文件 (SMB) 实现 Windows 的 Azure VM 上的 SAP NetWeaver 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2021
ms.author: radeltch
ms.openlocfilehash: 97564fef2278861f2e22e85752cd803ad289c766
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737373"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>使用适用于 SAP 应用程序的 Azure NetApp 文件 (SMB) 实现 Windows 上 Azure VM 的 SAP NetWeaver 高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[high-availability-guide]:high-availability-guide.md

[anf-azure-doc]:../../../azure-netapp-files/azure-netapp-files-introduction.md
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文介绍如何使用 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)上的 [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 部署和配置虚拟机、安装群集框架，以及在 Windows VM 上安装具有高可用性的 SAP NetWeaver 7.50 系统。  

文本不会详细介绍数据库层。 假设已创建 Azure [虚拟网络](../../../virtual-network/virtual-networks-overview.md)。  

请先阅读以下 SAP 说明和文档：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明 [1928533][1928533]，其中包含：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上所需的适用于 Windows 的 SAP 内核版本
* SAP 说明 [2015553][2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2178632][2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [1999351][1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 说明 [2287140](https://launchpad.support.sap.com/#/notes/2287140) 列出了 SAP 支持的 SMB 3.x 协议 CA 功能的先决条件。
* SAP 说明 [2802770](https://launchpad.support.sap.com/#/notes/2802770) 提供了针对 Windows 2012 和 2016 上 SAP transaction AL11 运行缓慢问题的故障排除信息。
* SAP 说明 [1911507](https://launchpad.support.sap.com/#/notes/1911507) 提供了针对 Windows Server 上文件共享（使用 SMB 3.0 协议）的透明故障转移功能的相关信息。
* SAP 说明 [662452](https://launchpad.support.sap.com/#/notes/662452) 提供了相关建议（停用 8.3 名称生成）来解决数据访问过程中出现的错误/处理文件系统性能不佳的情况。
* [在 Windows 故障转移群集上安装 SAP NetWeaver 高可用性，在 Azure 上安装适用于 SAP ASCS/SCS 实例的文件共享](./sap-high-availability-installation-wsfc-file-share.md) 
* [适用于 SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案](./sap-high-availability-architecture-scenarios.md)
* [在 ASCS 群集配置中添加探测端口](sap-high-availability-installation-wsfc-file-share.md)
* [在故障转移群集上安装 (A)SCS 实例](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [创建用于 Azure NetApp 文件的 SMB 卷](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]

> [!IMPORTANT]
> 注意：在托管于[ Azure NetApp 文件][anf-azure-doc] SMB 卷上的 SMB 共享中使用 SWPM 安装 SAP 系统可能会失败，并出现有关“权限不足”的安装错误（例如“warningPerm 未定义”）。 若要避免此错误，执行上下文 SWPM 所用的用户身份在安装 SAP 系统期间需要拥有提升的特权“域管理员”。  

## <a name="overview"></a>概述

SAP 开发了可以取代群集共享磁盘的新方法和新方案，用于将 Windows 故障转移群集上的 SAP ASCS/SCS 实例群集化。 可以使用 SMB 文件共享来部署 SAP 全局主机文件，而不使用群集共享磁盘。 Azure NetApp 文件将 Active Directory 和 NTFS ACL 配合使用来支持 SMBv3（以及 NFS）。 Azure NetApp 文件自然而然具备高可用性（因为它是 PaaS 服务）。 这些功能使得 Azure NetApp 文件非常适合用于托管 SAP 全局 SMB 文件共享。  
[Azure Active Directory (AD) 域服务](../../../active-directory-domain-services/overview.md)和 [Active Directory 域服务 (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 均受支持。 可以将现有 Active Directory 域控制器与 Azure NetApp 文件一起使用。 域控制器可以作为虚拟机放在 Azure 中，也可以通过 ExpressRoute 或 S2S VPN 放在本地。 本文将在 Azure VM 中使用域控制器。  
SAP Netweaver 中心服务的高可用性 (HA) 需要共享存储。 过去，若要在 Windows 上实现此目的，需要构建 SOFS 群集，或使用 SIOS 之类的群集共享磁盘软件。 现在，可以使用部署在 Azure NetApp 文件上的共享存储来实现 SAP Netweaver HA。 将 Azure NetApp 文件用于共享存储就无需使用 SOFS 或 SIOS。  

> [!NOTE]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。  

![采用 SMB 共享的 SAP ASCS/SCS HA 体系结构](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 文件共享的先决条件为：
* SMB 3.0（或更高版本）协议。
* 能够设置 Active Directory 用户组和 computer$ 计算机对象的 Active Directory 访问控制列表 (ACL)。
* 必须为文件共享启用 HA。

此参考体系结构中 SAP Central Services 的共享由 Azure NetApp 文件提供：

![采用 SMB 共享的 SAP ASCS/SCS HA 体系结构的详细信息](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>创建并装载用于 Azure NetApp 文件的 SMB 卷

执行以下步骤，以准备好使用 Azure NetApp 文件。  

1. 按照[注册 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-register.md)的步骤操作  
2. 按照[创建 NetApp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中所述的步骤创建 Azure NetApp 帐户  
3. 按照[设置容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明设置容量池
4. Azure NetApp 文件资源必须驻留在委托的子网中。 按照[将子网委托给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明创建委托的子网。  

   > [!IMPORTANT]
   > 在创建 SMB 卷之前，需要创建 Active Directory 连接。 查看 [Active Directory 连接要求](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)。  
   >   
   > 创建 Active Directory 连接时，请确保输入的 SMB 服务器（计算机帐户）前缀不超过 8 个字符，以避免违反 SAP 应用程序的 13 个字符主机名限制（会自动在 SMB 计算机帐户名称中添加一个后缀）。     
   > SAP 应用程序的主机名限制在 [2718300 - 物理和虚拟主机名长度限制](https://launchpad.support.sap.com/#/notes/2718300)和 [611361 - SAP ABAP 平台服务器的主机名](https://launchpad.support.sap.com/#/notes/611361)中进行了介绍。  

5. 按照[创建 Active Directory 连接](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)中所述创建 Active Directory 连接  
6. 按照[添加 SMB 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)中的说明创建 SMB Azure NetApp 文件 SMB 卷  
7. 在 Windows 虚拟机上装载 SMB 卷。

> [!TIP]
> 可以通过在 [Azure 门户](https://portal.azure.com/#home)中导航到“Azure NetApp 文件”对象，单击“卷”边栏选项卡，然后单击“装载说明”，找到有关如何装载 Azure NetApp 文件卷的说明 。  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>使用 Windows 故障转移群集准备 SAP HA 的基础结构 

1. [为 Azure 内部负载均衡器设置 ASCS/SCS 负载均衡规则](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716)。
2. [将 Windows 虚拟机添加到域](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c)。
3. [在 SAP ASCS/SCS 实例的两个群集节点上添加注册表条目](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [为 SAP ASCS/SCS 实例设置 Windows Server 故障转移群集](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. 如果使用的是 Windows Server 2016，我们建议配置 [Azure 云见证](/windows-server/failover-clustering/deploy-cloud-witness)。


## <a name="install-sap-ascs-instance-on-both-nodes"></a>在两个节点上安装 SAP ASCS 实例

需要 SAP 提供的以下软件：
   * SAP 软件预配管理器 (SWPM) 安装工具版本 SPS25 或更高版本。
   * SAP 内核 7.49 或更高版本
   * 根据[为群集化 SAP ASCS/SCS 实例创建虚拟主机名](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097)中所述，为群集化 SAP ASCS/SCS 实例创建虚拟主机名（群集网络名称）。

> [!NOTE]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>在第一个 ASCS/SCS 群集节点上安装 ASCS/SCS 实例

1. 在第一个群集节点上安装 SAP ASCS/SCS 实例。 启动 SAP SWPM 安装工具，然后导航到“产品” > “DBMS”>“安装”>“应用程序服务器 ABAP”（或“Java”）>“高可用性系统”>“ASCS/SCS 实例”>“第一个群集节点” 。  

2. 在 SWPM 中选择“文件共享群集”作为群集共享配置。  
3. 当步骤“SAP 系统群集参数”出现提示时，请输入已作为“文件共享主机名”创建的 Azure NetApp 文件 SMB 共享主机名 。  在此示例中，SMB 共享主机名为 anfsmb-9562。 

   > [!IMPORTANT]
   > 如果 SWPM 中的“先决条件检查程序结果”显示持续可用性功能的条件未被满足，可以按照[尝试访问 Windows 中不再存在的共享文件夹时错误消息延迟](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)中的说明解决此问题。  

   > [!TIP]
   > 如果 SWPM 中的“先决条件检查程序结果”显示“交换空间大小”条件未被满足，可以通过导航到“我的电脑”>“系统属性”>“性能设置”>“高级”>“虚拟内存”>“更改”来调整交换空间大小。  

4. 使用 PowerShell 配置 SAP 群集资源：`SAP-SID-IP` 探测端口。 请根据[配置探测端口](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)中所述，在某个 SAP ASCS/SCS 群集节点上执行此配置。

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>在第二个 ASCS/SCS 群集节点上安装 ASCS/SCS 实例

1. 在第二个群集节点上安装 SAP ASCS/SCS 实例。 启动 SAP SWPM 安装工具，然后导航到“产品” > “DBMS”>“安装”>“应用程序服务器 ABAP”（或“Java”）>“高可用性系统”>“ASCS/SCS 实例”>“附加群集节点” 。  

### <a name="update-the-sap-ascsscs-instance-profile"></a>更新 SAP ASCS/SCS 实例配置文件

更新 SAP ASCS/SCS 实例配置文件 \<SID>ASCS/SCS\<Nr>\<Host> 中的参数。


| 参数名称 | 参数值 |
| --- | --- |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

仅当使用 ENSA1 时才需要参数 `enque/encni/set_so_keepalive`。  
重启 SAP ASCS/SCS 实例。 按照[在 SAP ASCS/SCS 实例的群集节点上设置注册表项][high-availability-guide]的说明，在两个 SAP ASCS/SCS 群集节点上设置 `KeepAlive` 参数。 

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>安装 DBMS 实例和 SAP 应用程序服务器

安装以下组件以完成 SAP 安装：

   * 一个 DBMS 实例  
   * 一个主 SAP 应用程序服务器  
   * 一个附加的 SAP 应用程序服务器  

## <a name="test-the-sap-ascsscs-instance-failover"></a>测试 SAP ASCS/SCS 实例故障转移 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>在群集节点 A 与群集节点 B 之间来回故障转移
在此测试方案中，我们将群集节点 sapascs1 称为节点 A，将群集节点 sapascs2 称为节点 B。

1. 验证群集资源是否在节点 A 上运行。![图 1：执行故障转移测试之前，在节点 A 上运行的 Windows Server 故障转移群集资源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 重启群集节点 A。SAP 群集资源将移到群集节点 B。![图 2：执行故障转移测试之后，在节点 B 上运行的 Windows Server 故障转移群集资源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>锁定条目测试

1. 验证 SAP 排队复制服务器 (ERS) 是否处于活动状态  
2. 登录到 SAP 系统，执行事务 SU01，然后在更改模式下打开一个用户 ID。 这会生成 SAP 锁定条目。  
3. 登录到 SAP 系统后，通过导航到事务 ST12 来显示该锁定条目。  
4. 将 ASCS 资源从群集节点 A 故障转移到群集节点 B。  
5. 验证在执行 SAP ASCS/SCS 群集资源故障转移之前生成的锁定条目是否已保留。  

![图 3：执行故障转移测试后已保留锁定条目](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

有关详细信息，请参阅 [Troubleshooting for Enqueue Failover in ASCS with ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)（排查 ERS 中 ASCS 的排队故障转移问题）

## <a name="optional-configurations"></a>可选配置

下图显示了运行 Microsoft Windows 故障转移群集（以减少 VM 总数）的 Azure VM 上的多个 SAP 实例。

这可以是 SAP ASCS/SCS 群集上的本地 SAP 应用程序服务器，也可以是 Microsoft SQL Server Always On 节点上的 SAP ASCS/SCS 群集角色。

> [!IMPORTANT]
> 不支持在 SQL Server Always On 节点上安装本地 SAP 应用程序服务器。
>

SAP ASCS/SCS 和 Microsoft SQL Server 数据库都是单一故障点 (SPOF)。 为了在 Windows 环境中保护这些 SPOF，使用了 Azure NetApp 文件 SMB。

虽然 SAP ASCS/SCS 的资源消耗较小，但建议将 SQL Server 或 SAP 应用程序服务器的内存配置减少 2 GB。

### <a name="sap-application-servers-on-wsfc-nodes-using-netapp-files-smb"></a><a name="5121771a-7618-4f36-ae14-ccf9ee5f2031"></a>使用 NetApp 文件 SMB 的 WSFC 节点上的 SAP 应用程序服务器

![图 4：Azure 中的 Windows Server 故障转移群集配置，包含 Windows NetApp 文件 SMB 和本地安装的 SAP 应用程序服务器][sap-ha-guide-figure-8007A]

> [!NOTE]
> 图为使用额外的本地磁盘。 对于不会在 OS 驱动器 (C:\) 上安装应用程序软件的客户，这是可选的
>
### <a name="sap-ascsscs-on-sql-server-always-on-nodes-using-azure-netapp-files-smb"></a><a name="01541cf2-0a03-48e3-971e-e03575fa7b4f"></a>使用 Azure NetApp 文件 SMB 的 SQL Server Always On 节点上的 SAP ASCS/SCS

> [!IMPORTANT]
> 不支持将 Azure NetApp 文件 SMB 用于任何 SQL Server 卷。
> 

![图：使用 Azure NetApp 文件 SMB 的 SQL Server Always On 节点上的 SAP ASCS/SCS][sap-ha-guide-figure-8007B]

> [!NOTE]
> 图为使用额外的本地磁盘。 对于不会在 OS 驱动器 (C:\) 上安装应用程序软件的客户，这是可选的
>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立 Azure 上的 SAP HANA（大型实例）高可用性并规划其 
* 灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]

[sap-ha-guide-figure-8007A]:./media/virtual-machines-shared-sap-high-availability-guide/ha-smb-as.png
[sap-ha-guide-figure-8007B]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sql-ascs-smb.png