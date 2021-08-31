---
title: Azure 上的 SAP：Azure VM 支持的方案
description: 包含 SAP 工作负载的 Azure 虚拟机支持的方案
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3975f23ad184ec04849fca57ef37d5fe57c2b48f
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272029"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 虚拟机支持的方案中的 SAP 工作负载
在 Azure 中设计 SAP NetWeaver、Business One、`Hybris` 或 S/4HANA 系统体系结构为使用各种体系结构和工具实现可缩放、高效且高度可用的部署创造了各种不同的机会。 不过，这存在一些限制，具体取决于所用的操作系统或 DBMS。 此外，在本地受支持的方案不一定在 Azure 中受到同等的支持。 本文档介绍受支持的非高可用性和高可用性配置，以及仅使用 Azure VM 的体系结构。 有关 [HANA 大型实例](./hana-overview-architecture.md)支持的方案，请查看 [HANA 大型实例支持的方案](./hana-supported-scenario.md)一文。 


## <a name="2-tier-configuration"></a>2 层配置
SAP 2 层配置被认为建立在由同一服务器或 VM 单元上运行的 SAP DBMS 和应用程序层组合而成的层的基础之上。 第二层被视为用户界面层。 采用 2 层配置时，DBMS 和 SAP 应用程序层共享 Azure VM 的资源。 因此，需要以一种避免让不同的组件争用资源的方式来配置这些组件。 此外，还需要小心不要过度订阅 VM 的资源。 此类配置不提供比各种相关 Azure 组件的 [Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)更高的高可用性。

此类配置的图形表示形式如下所示：

![简单 2 层配置](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

用于生产和非生产案例的 Windows、Red Hat、SUSE、适用于 SQL Server DBMS 系统的 Oracle Linux、Oracle、DB2、maxDB 和 SAP ASE 支持此类配置。 对于用作 DBMS 的 SAP HANA，只有非生产案例支持此类配置。 这也包括 [Azure HANA 大型实例](./hana-overview-architecture.md)的部署案例。
对于 Azure 上支持的所有 OS/DBMS 组合，支持此类配置。 但是，必须以一种可以避免 DBMS 和 SAP 组件争用内存和 CPU 资源，从而避免超出可用物理资源的方式，来设置 DBMS 和 SAP 组件的配置。 需要通过限制允许 DBMS 分配的内存来实现此目的。 还需要限制应用程序实例上的 SAP 扩展内存。 此外，还需要监视 VM 的总体 CPU 消耗量，确保组件不会尽其最大限度消耗 CPU 资源。 

> [!NOTE]
> 对于生产 SAP 系统，我们建议根据本文档稍后所述，采用额外的高可用性和最终灾难恢复配置


## <a name="3-tier-configuration"></a>3 层配置
在此类配置中，需要将 SAP 应用程序层和 DBMS 层隔离到不同的 VM 中。 通常，对于较大的系统，以及出于更灵活地分配 SAP 应用程序层的资源的原因，你会采取这种做法。 在最简单的设置中，不会提供比各种相关 Azure 组件的 [Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)更高的高可用性。 

图形表示形式如下所示：

![显示简单 3 层配置的示意图。](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

用于生产和非生产案例的 Windows、Red Hat、SUSE、适用于 SQL Server DBMS 系统的 Oracle Linux、Oracle、DB2、SAP HANA、maxDB 和 SAP ASE 支持此类配置。 这是 [Azure HANA 大型实例](./hana-overview-architecture.md)的默认部署配置。 为方便起见，我们未区分 SAP 应用程序层中的 SAP Central Services 和 SAP 对话实例。 在此简单的 3 层配置中，不为 SAP Central Services 提供高可用性保护。

> [!NOTE]
> 对于生产 SAP 系统，我们建议根据本文档稍后所述，采用额外的高可用性和最终灾难恢复配置


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>每个 VM 或 HANA 大型实例单元有多个 DBMS 实例
在此配置类型中，将为每个 Azure VM 或 HANA 大型实例单元托管多个 DBMS 实例。 这种方案的动机可能是减少要维护的操作系统，并凭此降低成本。 其他动机可能是通过在多个 DBMS 实例之间共享较大 VM 或 HANA 大型实例单元的资源，来提高灵活性和效率。 到目前为止，主要是非生产系统采用这些配置。

此类配置如下所示：

![一个单元中的多个 DBMS 实例](./media/sap-planning-supported-configurations/multiple-database-instances.png)

以下系统支持此类 DBMS 部署：

- Windows 上的 SQL Server
- IBM Db2。 在 [多个实例（Linux、UNIX）](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)一文中可以找到详细信息
- Oracle。 有关详细信息，请参阅 [SAP 支持说明 #1778431](https://launchpad.support.sap.com/#/notes/1778431) 和相关 SAP 说明
- 对于 SAP HANA，支持一个 VM 上的多个实例（SAP 将此部署方法称为 MCOS）。 有关详细信息，请参阅 SAP 文章 [一台主机上的多个 SAP HANA 系统 (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

在一台主机上运行多个数据库实例时，需要确保不同的实例不会争用资源，从而导致超出 VM 的物理资源限制。 需要限制任一共享 VM 的实例可分配的内存时，尤其需要在内存方面做到这一点。 对于不同数据库实例可以使用的 CPU 资源，可能也是如此。 提到的所有 DBMS 都采用允许在实例级别限制内存分配和 CPU 资源的配置。
要使 Azure VM 支持此类配置，不同实例管理的数据库的数据文件和日志/重做日志文件所用的磁盘或卷应该是独立的。 换言之，不同 DBMS 实例管理的数据库的数据文件或日志/重做日志文件不应共享相同的磁盘或卷。 

HANA 大型实例的磁盘配置已提供，请参阅 [HANA 大型实例支持的方案](./hana-supported-scenario.md#single-node-mcos)中的详细信息。 

> [!NOTE]
> 对于生产 SAP 系统，我们建议根据本文档稍后所述，采用额外的高可用性和最终灾难恢复配置。 此文档稍后将介绍不支持高可用性配置的包含多个 DBMS 实例的 VM。


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>一个 VM 中的多个 SAP 对话实例
在许多情况下，会在裸机服务器甚至是私有云中运行的 VM 上部署多个对话实例。 采用此类配置的原因是，根据特定的工作负载、业务功能或工作负载类型定制特定的 SAP 对话实例。 不将这些实例隔离到不同 VM 的原因是避免增加操作系统维护和操作的工作量。 或者，在很多情况下，VM 的托管商或运营商要求按照操作和管理的每个 VM 支付每月费用。 在 Azure 中，在单个 VM 上托管多个 SAP 对话实例的方案支持用于 Windows、Red Hat、SUSE 和 Oracle Linux 操作系统上的生产和非生产目的。 如果有多个 SAP 应用程序服务器实例在单个 VM 上运行，则应设置可在 Windows 和新式 Linux 内核中使用的 SAP 内核参数 PHYS_MEMSIZE。 此外，建议限制操作系统上的 SAP 扩展内存扩展，例如，在实现 SAP 扩展内存自动增长的 Windows 中。 可以使用 SAP 配置文件参数 `em/max_size_MB` 中做出这种限制。

在 Azure VM 中运行多个 SAP 对话实例的 3 层配置如下所示：

![显示在 Azure VM 中运行多个 SAP 对话实例的 3 层配置的示意图。](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

为方便起见，我们未区分 SAP 应用程序层中的 SAP Central Services 和 SAP 对话实例。 在此简单的 3 层配置中，不为 SAP Central Services 提供高可用性保护。 对于生产系统，不建议使 SAP Central Services 保持不受保护状态。 有关适用于 SAP 中心实例的所谓多 SID 配置以及此类多 SID 配置的高可用性的具体信息，请参阅本文档稍后的部分。

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS 层的高可用性保护
想要部署 SAP 生产系统时，需要考虑热备用类型的高可用性配置。 尤其是对于需要先将数据载入内存才能全面恢复性能和可伸缩性的 SAP HANA，Azure 服务修复并非理想的高可用性措施。 

一般情况下，Microsoft 仅支持 docs.microsoft.com 中“SAP 工作负载”部分下所述的高可用性配置和软件包。 可以在 SAP 说明 [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 中阅读相同的陈述。 对于与 SAP 工作负载结合使用的、未在 Microsoft 文档中阐述的其他高可用性第三方软件框架，Microsoft 不会提供支持。 在这种情况下，高可用性框架的第三方供应商即是高可用性配置的支持方，而作为客户，你需要在支持过程中与该供应商接洽。 本文稍后会提到例外情况。 

一般而言，Microsoft 支持 Azure VM 或 HANA 大型实例单元上有限的一组高可用性配置。 有关 HANA 大型实例支持的方案，请参阅文档 [HANA 大型实例支持的方案](./hana-supported-scenario.md)。

对于 Azure VM，在 DBMS 级别支持以下高可用性配置：

- SUSE 和 Red Hat 上基于 Linux Pacemaker 的 SAP HANA 系统复制。 请参阅详情文章：
    - [SUSE Linux Enterprise Server 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability.md)
    - [Red Hat Enterprise Linux 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md)
- SUSE 和 Red Hat 上使用 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)的 SAP HANA 横向扩展 n+m 配置。 以下文章中列出了详细信息：
    - [使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [使用 Red Hat Enterprise Linux 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 基于 Windows 横向扩展文件服务的 SQL Server 故障转移群集。 不过，对于生产系统，建议使用 SQL Server Always On 而不是群集。 SQL Server Always On 使用独立的存储提供更高的可用性。 以下文章中提供了详细信息： 
    - [在 Azure 虚拟机上配置 SQL Server 故障转移群集实例](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On 支持适用于 Azure 上的 SQL Server 的 Windows 操作系统。 这是为 Azure 上的生产 SQL Server 实例建议的默认操作系统。 以下文章中提供了详细信息：
    - [介绍 Azure 虚拟机上的 SQL Server Always On 可用性组](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)。
    - [在位于不同区域的 Azure 虚拟机上配置 Always On 可用性组](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)。
    - [在 Azure 中为 Always On 可用性组配置负载均衡器](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)。
- 适用于 Windows 和 Oracle Linux 的 Oracle Data Guard。 可在以下文章中找到 Oracle Linux 的详细信息：
    - [在 Azure Linux 虚拟机上实现 Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
- 下面提供了使用 Pacemaker 的 SUSE 和 RHEL 上的 IBM Db2 HADR 详细文档：
    - [采用 Pacemaker 的 SUSE Linux Enterprise Server 上 Azure VM 中 IBM Db2 LUW 的高可用性](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server 上 Azure VM 中 IBM Db2 LUW 的高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)
- 以下文档中详细介绍了 SAP ASE 和 SAP maxDB 配置：
    - [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](./dbms_guide_sapase.md)
    - [Azure VM 上的 SAP MaxDB、liveCache 和内容服务器部署](./dbms_guide_maxdb.md)
- 以下文档中详细介绍了 HANA 大型实例高可用性方案：
    - [HANA 大型实例支持的方案 - 使用 HSR 和 STONITH 实现高可用性](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [HANA 大型实例支持的方案 - 主机自动故障转移 (1+1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> 对于上述任何方案，我们都不支持在一个 VM 中配置多个 DBMS 实例。 也就是说，对于每个案例，在每个 VM 上只能部署一个数据库实例，并使用所述的高可用性方法对其进行保护。 目前不支持在同一 Windows 或 Pacemaker 故障转移群集下保护多个 DBMS 实例。 此外，只有每个 VM 部署包含单个实例的案例才支持 Oracle Data Guard。 

有多种数据库系统允许在一个 DBMS 实例下托管多个数据库。 与使用 SAP HANA 时一样，可以在多个数据库容器 (MDC) 中托管多个数据库。 如果这些多数据库配置在一个故障转移群集资源中运行，则支持这些配置。 不支持的配置是需要多个群集资源的配置。 例如，要在一个 SQL Server 实例下定义多个 SQL Server 可用性组的配置。


![DBMS HA 配置](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

根据所用的 DBMS 和/或操作系统，不一定需要将 Azure 负载均衡器等组件配置为解决方案体系结构的一部分。 

具体而言，对于 maxDB，存储配置需是不同的。 在 maxDB 中，数据文件和日志文件需要位于用于高可用性配置的共享存储中。 仅对于 maxDB 而言，高可用性支持共享存储。 对于所有其他 DBMS，在每个节点上使用单独的存储堆栈是唯一支持的磁盘配置。

已知存在并且也可以在 Microsoft Azure 上运行的其他高可用性框架。 但是，Microsoft 未测试这些框架。 如果你要使用这些框架构建高可用性配置，需要与该软件的提供商协作来处理以下事宜：

- 开发部署体系结构
- 部署体系结构
- 为体系结构提供支持

> [!IMPORTANT]
> Microsoft Azure 市场提供各种软设备，它们在 Azure 原生存储的顶层提供存储解决方案。 这些软设备还可用于创建 NFS 共享，从理论上讲，在需要备用节点的 SAP HANA 扩展部署中可以使用这些共享。 出于各种原因，Microsoft 以及 Azure 上的 SAP 提供的任何 DBMS 部署都不支持这些存储软设备。 目前完全不支持 SMB 共享上的 DBMS 部署。 NFS 共享上的 DBMS 部署仅限于 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)中的 NFS 4.1 共享。


## <a name="high-availability-for-sap-central-service"></a>SAP Central Services 的高可用性
SAP Central Services 是 SAP 配置的另一个单一故障点。 因此，也需要保护这些 Central Services 进程。 SAP 工作负载支持的并在文档中阐述的产品/服务如下所述：

- 为 sapmnt 和全局传输目录使用 Windows 横向扩展文件服务的 Windows 故障转移群集服务器。 以下文章中提供了详细信息：
    - [使用 Azure 中的文件共享在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例](./sap-high-availability-guide-wsfc-file-share.md)
    - [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)
- 为 sapmnt 和全局传输目录使用基于 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)的 SMB 共享的 Windows 故障转移群集服务器。 以下文章中列出了详细信息：
    - [使用适用于 SAP 应用程序的 Azure NetApp 文件 (SMB) 实现 Windows 的 Azure VM 上的 SAP NetWeaver 高可用性](./high-availability-guide-windows-netapp-files-smb.md)
- 基于 SIOS `Datakeeper` 的 Windows 故障转移群集服务器。 尽管 Microsoft 在文档中做了介绍，但你需要与 SIOS 建立支持关系，以便在使用此解决方案时可与 SIOS 支持人员接洽。 以下文章中提供了详细信息：
    - [使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [针对 SAP ASCS/SCS 使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- 在 SUSE 操作系统上使用 Pacemaker，并使用两个 SUSE VM 以及用于文件复制的 `drdb` 创建高可用性 NFS 共享。 以下文章中提供了详细信息
    - [SUSE Linux Enterprise Server for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](./high-availability-guide-suse-nfs.md)
- 在 SUSE 操作系统上使用 Pacemaker，并利用 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)提供的 NFS 共享。 以下文章中提供了详细信息
    - [带有适用于 SAP 应用程序的 Azure NetApp 文件的 SUSE Linux Enterprise Server 上 Azure VM 上的 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)
- 在 Red Hat 操作系统上使用 Pacemaker，并在 `glusterfs` 群集上托管 NFS 共享。 可在以下文章中找到详细信息
    - [Azure 虚拟机在 Red Hat Enterprise Linux 上为 SAP NetWeaver 提供的高可用性](./high-availability-guide-rhel.md)
    - [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 `GlusterFS`](./high-availability-guide-rhel-glusterfs.md)
- 在 Red Hat 操作系统上使用 Pacemaker，并在 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)上托管 NFS 共享。 以下文章中提供了详细信息
    - [使用适用于 SAP 应用程序的 Azure NetApp 文件实现 Red Hat Enterprise Linux 上的 SAP NetWeaver 的 Azure 虚拟机高可用性](./high-availability-guide-rhel-netapp-files.md)

对于列出的解决方案，你需要与 SIOS 建立支持关系，以便为 `Datakeeper` 产品提供支持，并在出现问题时直接与 SIOS 接洽。 根据 Windows、Red Hat 和/或 SUSE 操作系统的授权方式，还可能需要与 OS 提供商签订支持合同，以获得所列高可用性配置的全面支持。

配置还可能如下所示：

![DBMS 和 ASCS HA 配置](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

图的右侧显示了高可用性 SAP Central Services。 除了使用故障转移群集框架（在出现问题时可实现故障转移）保护 SAP Central Services 以外，还有必要部署高可用性 NFS 或 SMB 共享或者 Windows 共享磁盘，以确保不管是否存在单个 VM，sapmnt 和全局传输目录都可供使用。 其他一些解决方案（例如 Windows 故障转移群集服务器和 Pacemaker）今后会要求 Azure 负载均衡器将流量定向或重定向到正常的节点。

在显示的列表中未提到 Oracle Linux 操作系统。 Oracle Linux 不支持将 Pacemaker 用作群集框架。 如果你想要在 Oracle Linux 上部署 SAP 系统并需要为 Oracle Linux 使用高可用性框架，则需要与第三方供应商协作。 其中一家供应商是 SIOS，他们的 Protection Suite for Linux 受 Azure 上的 SAP 支持。 有关详细信息，请参阅 SAP 说明 [#1662610 - SIOS Protection Suite for Linux 的支持详细信息](https://launchpad.support.sap.com/#/notes/1662610)。



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>上面列出的 SAP Central Services 方案支持的存储
由于只有一部分 Azure 存储类型提供质量符合要求的、适合在 SAP Central Services 群集方案中使用的高可用性 NFS 或 SMB 共享，因此下面列出了支持的存储类型

- 可以在除 Azure NetApp 文件以外的所有原生 Azure 存储类型上部署 Windows 故障转移群集服务器和 Windows 横向扩展文件服务器。 但是，我们建议使用高级存储，因为它在吞吐量和 IOPS 方面提供优越的服务级别协议。
- 支持在 Azure NetApp 文件上部署 Windows 故障转移群集服务器和 Azure NetApp 文件上的 SMB。 目前不支持 Azure 文件服务中的 SMB 共享。
- 可以在除 Azure NetApp 文件以外的所有原生 Azure 存储类型上部署 Windows 故障转移群集服务器和基于 SIOS `Datakeeper` 的 Windows 共享磁盘。 但是，我们建议使用高级存储，因为它在吞吐量和 IOPS 方面提供优越的服务级别协议。
- 支持在 Azure NetApp 文件上部署使用 Azure NetApp 文件上的 NFS 共享的 SUSE 或 Red Hat Pacemaker。 
- 支持使用原生 Azure 存储类型（Azure NetApp 文件除外）在两个 VM 之间部署使用 `drdb` 配置的 SUSE Pacemaker。 但是，我们建议使用高级存储，因为它在吞吐量和 IOPS 方面提供优越的服务级别协议。
- 支持使用原生 Azure 存储类型（Azure NetApp 文件除外）部署使用 `glusterfs` 提供 NFS 共享的 Red Hat Pacemaker。 但是，我们建议使用高级存储，因为它在吞吐量和 IOPS 方面提供优越的服务级别协议。

> [!IMPORTANT]
> Microsoft Azure 市场提供各种软设备，它们在 Azure 原生存储的顶层提供存储解决方案。 这些软设备也可用于创建 NFS 或 SMB 共享，从理论上讲，还可以在已组建故障转移群集的 SAP Central Services 中使用这些共享。 Microsoft 不直接支持对 SAP 工作负载使用这些解决方案。 如果你决定使用此类解决方案创建 NFS 或 SMB 共享，则对 SAP Central Services 配置的支持需要由拥有存储软设备中的软件的第三方来提供。


## <a name="multi-sid-sap-central-services-failover-clusters"></a>多 SID SAP Central Services 故障转移群集
为了减少大型 SAP 环境中所需的 VM 数量，SAP 允许在故障转移群集配置中运行多个不同 SAP 系统的 SAP Central Services 实例。 假设你有 30 个或者更多个 NetWeaver 或 S/4HANA 生产系统。 在未组建多 SID 群集的情况下，这些配置需要 30 个或者更多个 Windows 或 Pacemaker 故障转移群集配置中，提供 60 个或者更多个 VM。 除了 DBMS 以外，还需要故障转移群集。 跨故障转移群集配置中的两个节点部署多个 SAP Central Services 实例可以显著减少 VM 数量。 但是，在单个双节点群集配置中部署多个 SAP Central Services 实例也会带来一些劣势。 群集配置中单个 VM 相关的问题同样适用于多个 SAP 系统。 对群集配置中运行的来宾 OS 的维护需要经过更多的协调，因为多个生产 SAP 系统会受到影响。 诸如 SAP LaMa 的工具不支持在其系统克隆过程中组建多 SID 群集。

在 Azure 上，使用 ENSA1 和 ENSA2 的 Windows 操作系统支持多 SID 群集配置。 建议不要在一个多 SID 群集上结合使用旧式排队复制服务体系结构 (ENSA1) 与新式体系结构 (ENSA2)。 以下文章中提供了有关此类体系结构的详细信息

- [使用 Azure 上的 Windows Server 故障转移群集和共享磁盘实现 SAP ASCS/SCS 实例多 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [在 Azure 上使用 Windows Server 故障转移群集和文件共享实现 SAP ASCS/SCS 实例的多 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

对于 SUSE，还支持基于 Pacemaker 的多 SID 群集。 到目前为止，该配置受以下方案的支持：

- 最多五个 SAP ASCS/SCS 实例
- 旧式排队复制服务体系结构 (ENSA1)
- 双节点 Pacemaker 群集配置

[适用于 SAP 应用程序的 SUSE Linux Enterprise Server 上 Azure VM 中的 SAP NetWeaver 的多 SID 高可用性指南](./high-availability-guide-suse-multi-sid.md)中阐述了此配置

包含排队复制服务器的多 SID 群集示意图如下所示

![显示包含排队复制服务器的多 SID 群集的示意图。](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA 横向扩展方案
[SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中列出的一部分已通过 HANA 认证的 Azure VM 支持 SAP HANA 横向扩展方案。 在“群集”列中标有“是”的所有 VM 均可用于 OLAP 或 S/4HANA 横向扩展。以下 Azure 存储类型支持无备用节点的配置： 

- Azure 高级存储，包括适用于 /hana/log 卷的 Azure 写入加速器
- [超级磁盘](../../disks-enable-ultra-ssd.md)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

适用于 OLAP 或 S/4HANA 的具有备用节点的 SAP HANA 横向扩展配置仅支持 Azure NetApp 文件上托管的 NFS 共享。

有关具有或没有备用节点的具体存储配置的更多信息，请查看以下文章：

- [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md) 
- [使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [使用 Red Hat Enterprise Linux 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP 支持说明 #2080991](https://launchpad.support.sap.com/#/notes/2080991)

有关 HANA 大型实例支持的 HANA 横向扩展配置的详细信息，请参阅以下文档：

- [具有备用节点的 HANA 大型实例横向扩展系统支持的方案](./hana-supported-scenario.md#scale-out-with-standby)
- [没有备用节点的 HANA 大型实例横向扩展系统支持的方案](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>灾难恢复方案
支持多种灾难恢复方案。 我们将灾难体系结构定义为应该为整个断网的 Azure 区域提供补偿的体系结构。 这意味着，我们需要将灾难恢复目标确定为另一个 Azure 区域，而不是运行 SAP 环境的目标。 我们在 DBMS 层和非 DBMS 层中区分方法和配置。 

### <a name="dbms-layer"></a>DBMS 层
对于 DBMS 层，支持使用 DBMS 本机复制机制（例如 Always On、Oracle Data Guard、Db2 HADR、SAP ASE Always-On 或 HANA 系统复制）的配置。 在这种情况下，复制流必须是异步的，而不像在单个 Azure 区域内部署的典型高可用性方案中那样是同步的。 [跨 Azure 区域的 SAP HANA 可用性](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)一文中介绍了此类受支持 DBMS 灾难恢复配置的典型示例。 该文章中的第二幅插图描绘了使用 HANA 的方案示例。 在此类方案中，SAP 应用程序支持的主要数据库都可以部署。

支持使用较小的 VM 作为灾难恢复区域中的目标实例，因为该 VM 不会遇到工作负载流量已满的情况。 为此需要注意以下事项：

- 较小的 VM 类型不能附加太多的磁盘
- 较小 VM 的网络和存储吞吐量较低
- 如果在一个 Azure 可用性集中收集不同的 VM，或者应该在 VM 的 M 系列与 Mv2 系列之间进行大小调整，则跨 VM 系列调整大小可能会造成问题
- 考虑数据库实例的 CPU 和内存消耗量，确保它能够以极短的延迟接收更改流，并有足够的 CPU 和内存资源，能够以极短的延迟对数据应用这些更改  

可在 [VM 大小](../../sizes.md)页面找到有关不同 VM 大小限制的更多详细信息

部署 DR 目标的另一种受支持方法是，在运行非生产 SAP 实例的非生产 DBMS 实例的 VM 上安装另一个 DBMS 实例。 此方法可能有点难度，因为需要确定应在 DR 方案中充当主要实例的特定目标实例所需的内存、CPU 资源、网络带宽和存储带宽。 尤其是在 HANA 中，强烈建议在共享主机上配置充当 DR 目标的实例，使数据不会预先加载到 DR 目标实例中。

有关 HANA 大型实例 DR 方案，请查看以下文档：

- [使用存储复制在单个节点上实现 DR](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [使用存储复制在单个节点上实现 DR（多用途）](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [使用存储复制在单个节点上实现 DR（多用途）](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [将 HSR 和 DR 与存储复制配合使用来实现高可用性](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [使用存储复制实现横向扩展和 DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [在单个节点上使用 HSR 实现 DR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [在单个节点上使用 HSR 实现 DR（成本优化）](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [使用 HSR 实现高可用性和灾难恢复](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [使用 HSR 实现高可用性和灾难恢复（成本优化）](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [使用 HSR 实现横向扩展和 DR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> 尚未对 SAP 工作负载中的 DBMS 部署测试 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 的使用情况。 因此，目前不支持对 SAP 系统的 DBMS 层使用 Azure Site Recovery。 未列出的其他 Microsoft 和 SAP 复制方法不受支持。 使用第三方软件在不同 Azure 区域之间复制 SAP 系统的 DBMS 层的做法需由该软件的供应商提供支持，而不能通过 Microsoft 和 SAP 支持渠道接受支持。 
 
## <a name="non-dbms-layer"></a>非 DBMS 层
对于 SAP 应用程序层以及所需的最终共享或存储位置，客户可以利用以下两种主要方案：

- 不要将第二个 Azure 区域中的灾难恢复目标用于任何生产或非生产目的。 在此方案中，最好是不要部署充当灾难恢复目标的 VM，并将映像以及对生产 SAP 应用程序层映像的更改复制到灾难恢复区域。 可执行此类任务的功能之一是 [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md)。 Azure Site Recovery 支持这种 Azure 到 Azure 的复制方案。 
- 灾难恢复目标是非生产系统实际使用的 VM。 整个 SAP 环境分散在两个不同的 Azure 区域之间，通常其生产系统位于一个区域，非生产系统位于另一个区域。 在很多客户部署中，客户的非生产系统等同于生产系统。 客户在应用程序层的非生产系统中预先安装了生产应用程序实例。 在故障转移时，非生产实例将会关闭，生产 VM 的虚拟名称将转移到非生产 VM（在 DNS 中分配新 IP 地址后），预先安装的生产实例将会启动

### <a name="sap-central-services-clusters"></a>SAP Central Services 群集
使用共享磁盘 (Windows)、SMB 共享 (Windows) 或 NFS 共享的 SAP Central Services 群集要更难复制一些。 在 Windows 端，Windows 存储复制是可能的解决方案。 在 Linux 上，rsync 是可行的解决方案。



## <a name="non-supported-scenario"></a>不支持的方案
Azure 体系结构上的 SAP 工作负载不支持某些方案。 “不支持”指的是 SAP 和 Microsoft 无法支持这些配置，需要将支持事宜移交给提供用于建立此类体系结构的软件的最终相关第三方。 其中两个类别是：

- 存储软设备：Azure 市场中提供了许多存储软设备。 某些供应商会提供自己的文档，介绍如何在 Azure 上使用这些与 SAP 软件相关的存储软设备。 涉及此类存储软设备的配置或部署的支持需要由这些存储软设备的供应商来提供。 [SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553) 中也表述了这一事实
- 高可用性框架：对于 Azure 上的 SAP 工作负载，仅支持使用 Pacemaker 和 Windows Server 故障转移群集作为高可用性框架。 如前所述，Microsoft 介绍了 SIOS `Datakeeper` 解决方案并制作了其文档。 尽管如此，SIOS `Datakeeper` 的组件需要通过 SIOS（作为提供这些组件的供应商）来提供支持。 SAP 还在不同的 SAP 说明中列出了其他已通过认证的高可用性框架。 其中的某些框架也通过了 Azure 第三方供应商的认证。 尽管如此，使用这些产品的配置需要由产品供应商提供支持。 不同的供应商集成到 SAP 支持流程的方式不同。 决定在 Azure 上部署的 SAP 配置中使用产品之前，应明确知道哪种支持流程最适合特定的供应商。
- 数据库文件驻留在共享磁盘上的共享磁盘群集不受支持（maxDB 除外）。 对于其他所有数据库，支持的解决方案是使用单独的存储位置，而不是使用 SMB、NFS 共享或共享磁盘来配置高可用性方案

不支持的其他方案如下所述：

- 像在 NetWeaver、S/4HANA 和 `Hybris` 等系统中那样，会在 SAP 通用体系结构中的 SAP 应用层与 SAP DBMS 层之间造成较高网络延迟的部署方案。 这包括：
    - 在本地部署一个层，并在 Azure 中部署另一个层
    - 在不同的 Azure 区域中部署系统的 SAP 应用层，而部署的层不是 DBMS 层
    - 在与 Azure 位于同一位置的数据中心内部署一个层，并在 Azure 中部署另一个层，只不过此类体系结构模式是由 Azure 原生服务提供的
    - 在 SAP 应用层与 DBMS 层之间部署网络虚拟设备
    - 将与 Azure 数据中心位于同一位置的数据中心内托管的存储用于 SAP DBMS 层或 SAP 全局传输目录
    - 在两家不同的云供应商处部署两个层。 例如，在 Oracle 云基础结构中部署 DBMS 层，并在 Azure 中部署应用层
- 多实例 HANA Pacemaker 群集配置
- Windows 支持通过适用于 SAP 数据库的 ANF 上的 SOFS 或 SMB 进行使用共享磁盘的 Windows 群集配置。 不过，我们建议使用特定数据库的本机高可用性复制，并使用单独的存储堆栈
- 在 Linux 上，支持使用位于 ANF 顶层的 NFS 共享中的数据库文件部署 SAP 数据库，但 SAP HANA、Oracle Linux 上的 Oracle 以及 SUSE 和 RedHat 上的 Db2 除外
- 在除 Windows 和 Oracle Linux 以外的任何其他来宾 OS 上部署 Oracle DBMS。 另请参阅 [SAP 支持说明 #2039619](https://launchpad.support.sap.com/#/notes/2039619)

我们未进行测试，因此没有经验的方案包括：

- 用于复制 DBMS 层 VM 的 Azure Site Recovery。 因此，我们建议利用数据库本机异步复制功能实现可能的灾难恢复配置
 

## <a name="next-steps"></a>后续步骤
参阅 [SAP NetWeaver 的 Azure 虚拟机规划和实施](./planning-guide.md)中的后续步骤




  
