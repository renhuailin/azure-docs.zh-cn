---
title: SAP 工作负荷规划和部署清单 | Microsoft Docs
description: 在 Azure 上规划 SAP 工作负载部署和部署工作负载的清单
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c91961eb6e89533ee5b995eb56fdd172d21b6f2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730176"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 上的 SAP 工作负载：规划和部署清单

该清单适用于将 SAP NetWeaver、S/4HANA 和 Hybris 应用程序迁移到 Azure 基础结构即服务的客户。 在整个项目进行期间，客户和/或 SAP 合作伙伴应查看该清单。 尤其要注意，许多检查都在项目开始时以及规划阶段完成。 部署完成后，对已部署的 Azure 基础结构或 SAP 软件版本进行直接更改可能会变得很复杂。

在项目进行过程中的关键里程碑查看清单。 这样做可以在小问题变成大问题之前及时发现问题并处理。 你还可以有足够的时间来重新设计和测试任何必要的更改。 请不要想当然地认为该清单已完成而不去核实。 根据具体情况，可能需要执行许多额外的检查。

该清单中包含的任务均基于 Azure。 例如，在迁移 Azure 平台或托管提供程序期间，SAP 应用程序接口可能会更改。

该清单也可用于已部署的系统。 自部署后，可能添加了新功能，例如写入加速器、可用性区域以及新的 VM 类型。 因此，定期查看该清单非常有用，可确保你了解 Azure 平台中的新功能。

## <a name="project-preparation-and-planning-phase"></a>项目准备和规划阶段
此阶段的任务是规划 SAP 工作负载到 Azure 平台的迁移。 在此阶段，至少需要创建以下文档，并定义和讨论以下迁移元素：

1. 简要设计文档。 此文档应包含以下内容：
    - 当前的 SAP 组件和应用程序清单，以及 Azure 上的目标应用程序清单。
    - 责任分配对照表 (RACI)，用于定义相关方的责任和分配。 先简要划定责任，然后在规划和首批部署中再具体细化。
    - 简要解决方案体系结构。
    - 关于要部署到哪个 Azure 区域的决定。 请参阅 [Azure 区域列表](https://azure.microsoft.com/global-infrastructure/regions/)。 要了解每个区域中提供了哪些服务，请参阅[各区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)。
    - 连接本地和 Azure 的网络体系结构。 请参阅 [Azure 的虚拟数据中心蓝图](/azure/architecture/vdc/)熟悉相关内容。
    - 在 Azure 中运行对业务影响大的数据的安全原则。 要了解数据安全，请参阅 [Azure 安全文档](../../../security/index.yml)。
2.  技术设计文档。 此文档应包含以下内容：
    - 解决方案框图。
    - Azure 中计算、存储和网络组件的大小调整。 有关如何对 Azure VM 进行 SAP 大小调整，请参阅 [SAP 
    -  说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 。
    - 业务连续性和灾难恢复体系结构。
    - 有关 OS、DB、内核和 SAP 支持包版本的详细信息。 Azure VM 不一定支持 SAP NetWeaver 或 S/4HANA 支持的每个 OS 版本。 DBMS 版本也是如此。 请查看以下资源进行调整，并在必要时升级 SAP 版本、DBMS 版本和 OS 版本，以确保 SAP 和 Azure 支持这些版本。 你需要具有 SAP 和 Azure 同时支持的版本才能从 SAP 和 Microsoft 获得全面支持。 必要时，需要规划某些软件组件的升级。 有关支持的 SAP、OS 和 DBMS 软件的更多详细信息，请参阅：
        - [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 此说明定义了 Azure VM 支持的最低 OS 版本。 同时定义了大多数非 HANA 数据库所需的最低数据库版本。 最后，它还解释了如何对 SAP 支持的 Azure VM 类型进行 SAP 大小调整。
        - [SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 此说明定义了有关 Azure 存储的支持策略以及需要与 Microsoft 建立的支持关系。
        - [SAP 支持说明 #2039619](https://launchpad.support.sap.com/#/notes/2039619)。 此说明定义了 Azure 的 Oracle 支持对照表。 对于 SAP 工作负载，Oracle 仅支持 Windows 和 Oracle Linux 作为 Azure 中的来兵操作系统。 此支持声明也适用于运行 SAP 实例的 SAP 应用程序层。 但是，Oracle 不支持在 Oracle Linux 中通过 Pacemaker 实现 SAP 中心服务的高可用性。 如果需要在 Oracle Linux 上实现 ASCS 的高可用性，需要利用适用于 Linux 的 SIOS 保护套件。 有关详细的 SAP 认证数据，请查看 SAP 支持说明 [#1662610 - 适用于 Linux 的 SIOS 保护套件的支持详细信息](https://launchpad.support.sap.com/#/notes/1662610)。 对于 Windows，支持将 SAP 所支持的用于 SAP 中心服务的 Windows Server 故障转移群集解决方案与 Oracle 一起用作 DBMS 层。
        - [SAP 支持说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。 此说明提供了不同 OS 版本上 SAP HANA 的支持对照表。
        - [SAP 网站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)上列出了 SAP HANA 支持的 Azure VM 和 [HANA 大型实例](./hana-overview-architecture.md)。
        - [SAP 产品可用性对照表](https://support.sap.com/en/)。
        - [SAP 支持说明 #2555629 - SAP HANA 2.0 动态分层 - 虚拟机监控程序和云支持](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 支持说明 #1662610 - 适用于 Linux 的 SIOS 保护套件的支持详细信息](https://launchpad.support.sap.com/#/notes/1662610)
        - 其他 SAP 特定产品的 SAP 说明。     
    - Azure 上的 Windows、SLES 和 RHEL 来宾操作系统均支持对 SAP 中心服务使用多 SID 群集配置。 注意，波及范围会增加你在这种多 SID 群集上放置的 ASCS/SCS 数量。 你可以在以下文章中找到各来宾 OS 对应的文档：
        - [使用 Azure 上的 Windows Server 故障转移群集和共享磁盘实现 SAP ASCS/SCS 实例多 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [在 Azure 上使用 Windows Server 故障转移群集和文件共享实现 SAP ASCS/SCS 实例的多 SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [适用于 SAP 应用程序多 SID 的 SUSE Linux Enterprise Server 上 Azure VM 中的 SAP NetWeaver 的高可用性指南](./high-availability-guide-suse-multi-sid.md)
        - [适用于 SAP 应用程序多 SID 的 Red Hat Enterprise Linux 的 Azure VM 上的 SAP NetWeaver 高可用性指南](./high-availability-guide-rhel-multi-sid.md)
    - 高可用性和灾难恢复体系结构。
        - 根据 RTO 和 RPO，定义需要怎样的高可用性和灾难恢复体系结构。
        - 为了在区域内实现高可用性，请查看所需的 DBMS 需要在 Azure 中提供哪些内容。 大多数 DBMS 包都提供同步热备用服务器的同步方法，建议将其用于生产系统。 另请查看不同数据库的 SAP 相关文档；请从[部署适用于 SAP 工作负载的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md)和相关文档开始。
           对于 DBMS 层，不支持采用共享磁盘配置的 Windows Server 故障转移群集，例如[针对 SQL Server 所述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)。 请改用如下解决方案：
           - [SQL Server AlwaysOn](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 数据防护](../oracle/configure-oracle-dataguard.md)
           - [HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - 对于跨 Azure 区域的灾难恢复，请查看不同 DBMS 供应商提供的解决方案。 其中大部分都支持异步复制或日志传送。
        - 对于 SAP 应用程序层，请确定是否要在同一 Azure 区域或 DR 区域中运行业务回归测试系统（理想情况下是生产部署的副本）。 在后一种情况下，你可以将该业务回归系统作为生产部署的 DR 目标。
        - 如果决定不在 DR 站点中放置非生产系统，可考虑通过 Azure Site Recovery 将 SAP 应用程序层复制到 Azure DR 区域。 有关详细信息，请参阅[为多层 SAP NetWeaver 应用部署设置灾难恢复](../../../site-recovery/site-recovery-sap.md)。
        - 如果决定通过使用 [Azure 可用性区域](../../../availability-zones/az-overview.md)来使用组合的 HADR 配置，请熟悉提供可用性区域的 Azure 区域。 还应考虑到两个可用性区域之间网络延迟增加可能带来的限制。  
3.  所有 SAP 接口（SAP 和非 SAP）的清单。
4.  基础服务的设计。 此设计应包括以下各项：
    - Active Directory 和 DNS 设计。
    - Azure 中的网络拓扑和不同 SAP 系统的分配。
    - 适用于在 Azure 中管理基础结构和 SAP 应用程序的团队的 [Azure 基于角色的访问控制 (Azure RBAC)](../../../role-based-access-control/overview.md) 结构。
    - 资源组拓扑。
    - [标记策略](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing)。
    - VM 及其他基础结构组件的命名约定和/或逻辑名称。
5.  Microsoft 专业或顶级支持合同。 如果与 Microsoft 签订了顶级支持合同，请确定你的 Microsoft 技术客户经理 (TAM)。 有关 SAP 支持要求，请参阅 [SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。
6.  Azure 订阅数以及订阅的核心配额。 根据需要[创建增加 Azure 订阅配额的支持请求](../../../azure-portal/supportability/regional-quota-requests.md)。
7.  将 SAP 数据迁移到 Azure 的数据缩减和数据迁移计划。 对于 SAP NetWeaver 系统，SAP 提供了有关如何限制大量数据量的指南。 请参阅[此 SAP 指南](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2)，了解 SAP ERP 系统中的数据管理。 一些内容通常也适用于 NetWeaver 和 S/4HANA 系统。
8.  自动部署方法。 在 Azure 上实现基础结构部署自动化的目标是以确定性的方式进行部署并获得确定性结果。 许多客户都使用基于 PowerShell 或 CLI 的脚本。 不过，你可以使用多种开源技术为 SAP 部署 Azure 基础结构，甚至安装 SAP 软件。 你可以在 GitHub 上找到相关示例：
    - [Azure 云中的自动 SAP 部署](https://github.com/Azure/sap-hana)
    - [SAP HANA 安装](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  定义客户、系统集成商、Microsoft 及其他相关方之间的常规设计和部署评审节奏。


## <a name="pilot-phase-strongly-recommended"></a>试点阶段（强烈推荐）
 
你可以在项目规划和准备之前或期间进行试点。 还可以利用试点阶段测试规划和准备阶段确定的方法和设计。 你可以延伸试点阶段，使其成为实际的概念证明。

建议在试点部署过程中构建并验证完整的 HADR 解决方案和安全设计。 一些客户会在此阶段执行可伸缩性测试。 另一些客户则将 SAP 沙盒系统的部署用作试点阶段。 假定你已经确定了要迁移到 Azure 以进行试点的系统。

1. 优化到 Azure 的数据传输。 最佳选择很大程度上取决于具体情况。 如果 ExpressRoute 线路有足够的带宽，则通过 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 从本地传输的速度最快。 在其他情况下，通过 Internet 进行传输的速度更快。
2. 对于涉及数据导出和导入的异类 SAP 平台迁移，请测试并优化导出和导入阶段。 对于 SQL Server 是目标平台的大型迁移，可以查找[建议](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)。 如果不需要在迁移的同时进行版本升级，可以使用迁移监视器/SWPM。 如果需要在迁移的同时进行 SAP 版本升级，可以使用 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 过程。 为此，需要满足源和目标 DBMS 平台组合的特定要求。 [SUM 2.0 SP03 的数据库迁移选项 (DMO)](https://launchpad.support.sap.com/#/notes/2631152) 中介绍了此过程。
   1.  导出到源、将导出的文件上传到 Azure 和导入性能。 使导出和导入之间的重叠最大化。
   2.  评估源和目标平台上的数据库数量，以调整基础结构大小。
   3.  验证并优化时间安排。
1. 技术验证。
   1. VM 类型。
        - 再次查看 SAP 支持说明、SAP HANA 硬件目录和 SAP PAM 中的资源。 确保支持的 Azure VM、这些 VM 类型支持的 OS 版本以及支持的 SAP 和 DBMS 版本没有发生更改。
        - 再次验证 Azure 上部署的应用程序和基础结构的大小。 如果要移动现有应用程序，通常可以从使用的基础结构和 [SAP 基准网页](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)中获取必要的 SAPS，并将其与 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中列出的 SAPS 编号进行比较。 另请注意[有关 SAPS 评级的这篇文章](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)。
        - 评估并测试 Azure VM 的大小调整，了解在规划阶段选择的 VM 类型的最大存储吞吐量和网络吞吐量。 你可以在以下位置找到这些数据：
           -  [Azure 中 Windows 虚拟机的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 重要的是在调整大小时要考虑“最大非缓存磁盘吞吐量”。
           -  [Azure 中 Linux 虚拟机的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 重要的是在调整大小时要考虑“最大非缓存磁盘吞吐量”。
   2. 存储。
        - 查看 [SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)一文
        - 对于代表 SAP 应用程序层的 VM 以及非性能敏感型 DBMS 的部署，至少应使用 [Azure 标准 SSD 存储](../../disks-types.md#standard-ssd)。
        - 通常，不建议使用 [Azure 标准 HDD 磁盘](../../disks-types.md#standard-hdd)。
        - 对于远程性能敏感型 DBMS VM，应使用 [Azure 高级存储](../../disks-types.md#premium-ssd)。
        - 使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。
        - 对于使用 M 系列的 DBMS 日志驱动器，请使用 Azure 写入加速器。 请注意写入加速器限制和用法，如[写入加速器](../../how-to-enable-write-accelerator.md)中所述。
        - 对于不同的 DBMS 类型，请查看[与 SAP 相关的 DBMS 通用文档](./dbms_guide_general.md)以及通用文档指向的 DBMS 特定文档。
        - 有关 SAP HANA 的详细信息，请参阅 [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md)。
        - 请勿使用设备 ID 将 Azure 数据磁盘装载到 Azure Linux VM 中。 而应该使用全局唯一标识符 (UUID)。 例如，使用图形化工具装载 Azure 数据磁盘时应小心。 反复检查 /etc/fstab 中的条目，确保使用 UUID 装载磁盘。 可在[这篇文章](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)中找到更多详细信息。
   3. 联网。
        - 测试和评估虚拟网络基础结构以及 SAP 应用程序在不同 Azure 虚拟网络之间或内部的分布情况。
        -  在单个 Azure 虚拟网络中评估中心和分支虚拟网络体系结构方法或微分段方法。 此评估基于：
               1. 在[对等互连的 Azure 虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)之间进行数据交换的成本。 有关成本的信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。
               2. 与更改网络安全组以隔离虚拟网络中的子网相比，快速断开 Azure 虚拟网络之间的对等互连的优势。 此评估适用于虚拟网络的子网中托管的应用程序或 VM 构成安全风险的情况。
                3. 本地、外部环境以及你在 Azure 中构建的虚拟数据中心之间的网络流量的集中日志记录和审核。
        - 评估和测试 SAP 应用程序层和 SAP DBMS 层之间的数据路径。
            -  不支持将 [Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)放置在 SAP 应用程序与基于 SAP NetWeaver、Hybris 或 S/4HANA 的 SAP 系统的 DBMS 层之间的通信路径中。
            -  不支持将 SAP 应用程序层和 SAP DBMS 放置在未对等互连的不同 Azure 虚拟网络中。
            -  可以使用[应用程序安全组和网络安全组规则](../../../virtual-network/network-security-groups-overview.md)来定义 SAP 应用程序层和 SAP DBMS 层之间的路由。
        - 确保在 SAP 应用程序层和 SAP DBMS 层中使用的 VM 上启用了 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 注意，需要不同的 OS 级别来支持 Azure 中的加速网络：
            - Windows Server 2012 R2 或更高版本。
            - SUSE Linux 12 SP3 或更高版本。
            - RHEL 7.4 或更高版本。
            - Oracle Linux 7.5。 如果使用 RHCKL 内核，则需要版本 3.10.0-862.13.1.el7。 如果使用 Oracle UEK 内核，则需要版本 5。
        - 根据 SAP 支持说明 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 测试和评估 SAP 应用程序层 VM 和 DBMS VM 之间的网络延迟。 根据 SAP 支持说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中的网络延迟指南评估结果。 网络延迟应为中等至良好。 VM 和 HANA 大型实例单元之间的通信例外（如[这篇文章](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)所述）。
        - 确保 ILB 部署设置为使用直接服务器返回。 如果将 Azure ILB 用于 DBMS 层上的高可用性配置，此设置将减少延迟。
        - 如果要将 Azure 负载均衡器与 Linux 来宾操作系统一起使用，请检查 Linux 网络参数 net.ipv4.tcp_timestamps 是否已设置为 0 。 此建议与旧版 [SAP 说明 #2382421](https://launchpad.support.sap.com/#/notes/2382421) 中的建议冲突。 该 SAP 说明现已更新，指出需要将此参数设置为 0 才能使用 Azure 负载均衡器。
        - 请考虑使用 [Azure 邻近放置组](../../co-location.md)以最大程度地降低网络延迟。 有关详细信息，请参阅[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)。
   4. 高可用性和灾难恢复部署。
        - 如果在未定义特定 Azure 可用性区域的情况下部署 SAP 应用程序层，请确保在[可用性集](../../availability-set-overview.md)中部署运行 SAP 对话实例或运行单个 SAP 系统的中间件实例的所有 VM。
        - 如果不需要 SAP 中心服务和 DBMS 的高可用性，则可以将这些 VM 部署到与 SAP 应用程序层相同的可用性集中。
        - 如果使用被动复制保护 SAP 中心服务和 DBMS 层以实现高可用性，请将 SAP 中心服务的两个节点放在一个单独的可用性集中，将两个 DBMS 节点放在另一个可用性集中。
        - 如果部署到 Azure 可用性区域，则无法利用可用性集。 但你确实需要确保将主动和被动中心服务节点部署到两个不同的可用性区域中。 请使用相互之间具有最低延迟的可用性区域。
          注意，如果跨可用性区域为 DBMS 和 SAP 中心服务层创建 Windows 或 Pacemaker 故障转移群集，则需要使用 [Azure 标准负载均衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 [基本负载均衡器](../../../load-balancer/load-balancer-overview.md)不能用于分区部署。
   5. 超时设置。
        - 查看 SAP 实例的 SAP NetWeaver 开发人员跟踪，并确保排队服务器和 SAP 工作进程之间没有出现连接中断。 通过设置以下两个注册表参数，可以避免这些连接中断：
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000。 有关详细信息，请参阅 [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))。
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000。 有关详细信息，请参阅 [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))。
        - 为了避免本地 SAP GUI 接口和 Azure 中部署的 SAP 应用程序层之间的 GUI 超时，请检查是否在 default.pfl 或实例配置文件中设置了以下参数：
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - 要防止 SAP 排队进程和 SAP 工作进程的连接中断，需要将 enque/encni/set_so_keepalive 参数设置为 true 。 另请参阅 [SAP 说明 #2743751](https://launchpad.support.sap.com/#/notes/2743751)。  
        - 如果使用 Windows 故障转移群集配置，请确保为 Azure 正确设置了用于对非响应节点做出响应的时间。 [调整故障转移群集网络阈值](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)一文列出了这些参数并介绍了这些参数如何影响故障转移敏感性。 假设群集节点位于同一子网中，你应该更改以下参数：
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OS 设置或修补程序
        - 要在 SAP 上运行 HANA，请阅读以下说明和文档：
            -   [SAP 支持说明 #2814271 - SAP HANA 备份在 Azure 上失败，出现校验和错误](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 支持说明 #2753418 - 由于计时器回退而导致潜在的性能下降](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 支持说明 #2791572 - 由于 Azure 中的 Hyper-v 缺少 VDSO 支持而导致性能下降](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP 支持说明 #2382421 - 优化 HANA 和 OS 级别的网络配置](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP 支持说明 #2694118 - Azure 上的 Red Hat Enterprise Linux HA 加载项](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP 支持说明 #1984787 - SUSE LINUX Enterprise Server 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 支持说明 #2002167 - Red Hat Enterprise Linux 7.x：安装和升级](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
            -   [SAP 支持说明 #2772999 - Red Hat Enterprise Linux 8.x：安装和升级](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP 支持说明 #2777782 - SAP HANA DB：RHEL 8 的建议 OS 设置](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 支持说明 #2578899 - SUSE Linux Enterprise Server 15：安装说明](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 支持说明 #2455582 - Linux：运行用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 支持说明 #2729475 - 经过 SAP HANA 认证的 Azure VM HWCCT 失败，出现错误“不支持虚拟机监控程序”](https://launchpad.support.sap.com/#/notes/2729475)
1. 测试高可用性和灾难恢复过程。
   1. 通过关闭 VM（Windows 来宾操作系统）或将操作系统置于紧急模式（Linux 来宾操作系统）来模拟故障转移情况。 此步骤可帮助你确定故障转移配置是否正常工作。
   1. 测量执行故障转移所需的时间。 如果时间太长，请考虑：
        - 对于 SUSE Linux，请使用 SBD 设备而非 Azure 隔离代理来加速故障转移。
        - 对于 SAP HANA，如果重新加载数据的时间太长，请考虑预配更多存储带宽。
   3. 测试备份/还原的顺序和时间，并在必要时进行更正。 确保备份时间足够。 还需要测试还原和确定还原活动的时间。 如果 RTO 依赖于数据库或 VM 还原过程，请确保还原时间在 RTO SLA 范围内。
   4. 测试跨区域 DR 功能和体系结构。
1. 安全检查。
   1. 测试 Azure 基于角色的访问控制 (Azure RBAC) 体系结构的有效性。 目标是分离和限制不同团队的访问和权限。 例如，SAP 基础团队成员应该可以部署 VM 并将 Azure 存储中的磁盘分配到给定的 Azure 虚拟网络中。 但是，SAP 基础团队应该不能创建自己的虚拟网络或更改现有虚拟网络的设置。 网络团队的成员应该不能将 VM 部署到运行 SAP 应用程序和 DBMS VM 的虚拟网络中。 此团队的成员应该也不能更改 VM 的属性，甚至删除 VM 或磁盘。  
   1.  验证 [网络安全组和 ASC](../../../virtual-network/network-security-groups-overview.md) 规则是否正常工作并保护相应的资源。
   1.  确保所有需要加密的资源都已加密。 定义和执行备份证书、存储和访问这些证书以及还原加密实体的过程。
   1.  从 OS 支持的角度来讲，尽可能对 OS 磁盘使用 [Azure 磁盘加密](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md)。
   1.  请确保不使用太多的加密层。 在某些情况下，将 Azure 磁盘加密与其中一种 DBMS 透明数据加密方法一起使用可以保护同一服务器上的不同磁盘或组件，这一点很有帮助。  例如，在 SAP DBMS 服务器上，可以在操作系统启动磁盘（如果操作系统支持 ADE）和 DBMS 数据暂留文件未使用的数据磁盘上启用 Azure 磁盘加密 (ADE)。  例如，在保存 DBMS TDE 加密密钥的磁盘上使用 ADE。
1. 性能测试。 在 SAP 中，根据 SAP 跟踪和测量进行以下比较：
   - 在适用的情况下，将前 10 个在线报告与当前实施进行比较。
   - 在适用的情况下，将前 10 个批处理作业与当前实施进行比较。
   - 比较通过接口进入 SAP 系统的数据传输。 重点是你知道的在不同位置之间（例如从本地到 Azure）传输数据的接口。


## <a name="non-production-phase"></a>非生产阶段 
在此阶段，假定你在成功试点或概念证明 (POC) 之后开始将非生产 SAP 系统部署到 Azure 中。 请将在 POC 中学到的经验教训应用到此部署中。 POC 的所有条件和步骤也适用于此部署。

在此阶段中，通常会将开发系统、单元测试系统和业务回归测试系统部署到 Azure。 建议在一个 SAP 应用程序系列中至少有一个非生产系统具有今后生产系统将具有的完整高可用性配置。 在此阶段，需要完成以下附加步骤：  

1.  在将系统从旧平台迁移到 Azure 之前，请收集资源消耗数据，例如 CPU 使用率、存储吞吐量和 IOPS 数据。 特别是从 DBMS 层单元收集这些数据，但也需要从应用程序层单元收集。 此外，还应测量网络和存储延迟。
2.  记录系统的可用时间情况。 目标是确定非生产系统是需要全天候可用，还是可以在一周或一个月的某些阶段关闭。
3.  测试并定义是为 Azure 中的 VM 创建自己的 OS 映像，还是使用 Azure 共享映像库中的映像。 如果使用共享映像库中的映像，请确保使用反映与 OS 供应商的支持合同的映像。 对于某些 OS 供应商，共享映像库允许你自带许可证映像。 对于其他 OS 映像，Azure 报价中包含此项支持。 如果决定创建自己的 OS 映像，可以在这些文章中找到文档：
    -   [为 Azure 中部署的 Windows VM 构建通用映像](../../windows/capture-image-resource.md)
    -   [为 Azure 中部署的 Linux VM 构建通用映像](../../linux/capture-image.md)
3.  如果使用共享映像库中的 SUSE 和 Red Hat Linux 映像，则需要使用 Linux 供应商在共享映像库中提供的用于 SAP 的映像。
4.  确保满足 Microsoft 支持协议的 SAP 支持要求。 请参阅 [SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 对于 HANA 大型实例，请参阅[加入要求](./hana-onboarding-requirements.md)。
4.  请确保相关人员收到[计划内维护通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)，以便你选择最佳停机时间。
5.  经常查看 [Channel 9](https://channel9.msdn.com/) 之类的频道上的 Azure 演示文稿，了解可能适用于你的部署的新功能。
6.  检查与 Azure 相关的 SAP 说明，比如新 VM SKU 或新支持的 OS 和 DBMS 版本的[支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 比较新 VM 类型与旧 VM 类型的定价，以便部署性价比最高的 VM。
7.  经常查看 SAP 支持说明、SAP HANA 硬件目录和 SAP PAM。 确保支持的 Azure VM、这些 VM 支持的 OS 版本以及支持的 SAP 和 DBMS 版本没有发生更改。
8.  查看 [SAP 网站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，了解 Azure 中经过 HANA 认证的新 SKU。 比较新 SKU 与计划使用的 SKU 的定价。 最后，进行必要的更改，以使用性价比最高的 SKU。
9.  调整部署脚本以利用新的 VM 类型，并整合你想使用的新 Azure 功能。
10. 完成基础结构部署后，根据 SAP 支持说明 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 SAP 支持说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 测试和评估 SAP 应用层 VM 与 DBMS VM 之间的网络延迟。 根据 SAP 支持说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中的网络延迟指南评估结果。 网络延迟应为中等至良好。 VM 和 HANA 大型实例单元之间的通信例外（如[这篇文章](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)所述）。 确保[部署适用于 SAP 工作负载的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md#azure-network-considerations)和 [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md)中提及的所有限制均不适用于你的部署。
11. 确保将 VM 部署到正确的 [Azure 邻近放置组](../../co-location.md)，如[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)中所述。
11. 在应用工作负载之前，请执行概念验证阶段的所有其他检查。
12. 在应用工作负载时，记录 Azure 中这些系统的资源消耗。 将此消耗与旧平台中的记录进行比较。 如果发现差异较大，请调整今后部署的 VM 大小。 注意，当你缩小规模时，VM 的存储和网络带宽也随之减少。
    - [Azure 中 Windows 虚拟机的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure 中 Linux 虚拟机的大小](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 试验系统复制功能和过程。 目标是使你能够轻松地复制开发系统或测试系统，以便项目团队能够快速地获得新系统。 
14. 优化和增强团队的 Azure 基于角色的访问、权限和过程，确保责任分离。 同时，确保所有团队都可以在 Azure 基础结构中执行各自的任务。
15. 练习、测试和记录高可用性和灾难恢复过程，使你的员工能够执行这些任务。 找出缺点并调整要整合到部署中的新 Azure 功能。


## <a name="production-preparation-phase"></a>生产准备阶段 
在此阶段，收集你在非生产部署中累积的经验和知识，并将它们应用于今后的生产部署。 你还需要为当前托管位置和 Azure 之间的数据传输做好准备。

1.  在迁移到 Azure 之前，先完成生产系统的必要 SAP 版本升级。
1.  与企业所有者就生产系统迁移后需要进行的功能和业务测试达成一致。
1.  确保使用当前托管位置中的源系统执行这些测试。 避免在系统移动到 Azure 后才首次执行测试。
1.  测试将生产系统迁移到 Azure 的过程。 如果未在同一时间范围内将所有生产系统迁移到 Azure，请创建需要位于同一托管位置的生产系统组。 测试数据迁移。 一些常见的方法如下：
    - 将 DBMS 方法（如备份/还原）与 SQL Server AlwaysOn、HANA 系统复制或日志传送结合使用，可以将数据库内容播种并同步到 Azure 中。
    - 对较小的数据库使用备份/还原。
    - 使用集成到 SAP SWPM 中的 SAP 迁移监视器来执行异类迁移。
    - 如果需要在迁移的同时进行 SAP 版本升级，请使用 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 过程。 注意，并非所有源 DBMS 和目标 DBMS 组合都受支持。 你可以在不同 DMO 版本的特定 SAP 支持说明中找到更多信息。 例如，[SUM 2.0 SP04 的数据库迁移选项 (DMO)](https://launchpad.support.sap.com/#/notes/2644872)。
    - 在需要移动备份或 SAP 导出文件的情况下，测试是通过 Internet 还是通过 ExpressRoute 进行数据传输更好。 如果通过 Internet 移动数据，则可能需要更改一些网络安全组/应用程序安全组规则，以应用于今后的生产系统。
1.  将系统从旧平台迁移到 Azure 之前，请收集资源消耗数据。 有用的数据包括 CPU 使用率、存储吞吐量和 IOPS 数据。 特别是从 DBMS 层单元收集这些数据，但也需要从应用程序层单元收集。 此外，还应测量网络和存储延迟。
1.  经常查看 SAP 支持说明以及所需的 OS 设置、SAP HANA 硬件目录和 SAP PAM。 确保支持的 Azure VM、这些 VM 支持的 OS 版本以及支持的 SAP 和 DBMS 版本没有发生更改。
1.  更新部署脚本，以反映你在 VM 类型和 Azure 功能方面的最新决定。
1.  部署基础结构和应用程序后，验证：
    - 使用正确的属性和存储大小部署了正确的 VM 类型。
    - VM 位于正确的所需 OS 版本和修补程序上，并且是统一的。
    - VM 根据需要统一进行了加固。
    - 安装并部署了正确的应用程序版本和修补程序。
    - VM 已按计划部署到了 Azure 可用性集中。
    - Azure 高级存储用于延迟敏感型磁盘或[单个 VM SLA 达到 99.9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 的情况。
    - 正确部署了 Azure 写入加速器。
        - 确保在 VM 内跨需要写入加速器的磁盘正确构建了存储空间或条带集。
        - 请参阅 [Linux 上的软件 RAID 配置](/previous-versions/azure/virtual-machines/linux/configure-raid)。
        - 请参阅 [Azure 中 Linux VM 上的 LVM 配置](/previous-versions/azure/virtual-machines/linux/configure-lvm)。
    - [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)是专用的。
    - VM 部署到了正确的可用性集和可用性区域中。
    - 确保在 SAP 应用程序层和 SAP DBMS 层中使用的 VM 上启用了 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
    - 不支持将 [Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)放置在 SAP 应用程序与基于 SAP NetWeaver、Hybris 或 S/4HANA 的 SAP 系统的 DBMS 层之间的通信路径中。
    - 应用程序安全组和网络安全组规则实现了按需要和计划进行通信，并在需要时阻止通信。
    - 正确设置了超时设置，如前面所述。
    - VM 部署到了正确的 [Azure 邻近放置组](../../co-location.md)，如[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)中所述。
    - 测试和验证了 SAP 应用程序层 VM 与 DBMS VM 之间的网络延迟，如 SAP 支持说明 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中所述。 根据 SAP 支持说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中的网络延迟指南评估结果。 网络延迟应为中等至良好。 VM 和 HANA 大型实例单元之间的通信例外（如[这篇文章](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)所述）。
    - 必要时使用适当的加密方法实施了加密。
    - 接口和其他应用程序可以连接新部署的基础结构。
1.  创建用于响应 Azure 计划内维护的 playbook。 确定重启系统和 VM 以进行计划内维护的顺序。
    

## <a name="go-live-phase"></a>上线阶段
在上线阶段，请确保遵循在之前阶段制定的 playbook。 执行已测试和练习的步骤。 不要接受最新的配置和过程更改。 并同时完成以下步骤：

1. 验证 Azure 门户监视和其他监视工具是否正常工作。 建议在 Windows 中使用性能监视器 (perfmon)，在 Linux 中使用 SAR。
    - CPU 计数器。
        - 平均 CPU 时间 - 总计（所有 CPU）
        - 平均 CPU 时间 - 每单个处理器（M128 VM 上的 128 个处理器）
        - CPU 内核时间 - 每单个处理器
        - CPU 用户时间 - 每单个处理器
    - 内存。
        - 免费内存
        - 传入的内存页/秒
        - 传出的内存页/秒
    - 。
        - 磁盘读取 (KBps) - 每单个磁盘
        - 磁盘读取数/秒 - 每单个磁盘
        - 磁盘读取时间（μs/次）- 每单个磁盘
        - 磁盘写入 (KBps) - 每单个磁盘
        - 磁盘写入数/秒 - 每单个磁盘
        - 磁盘写入时间（μs/次）- 每单个磁盘
    - Network.
        - 传入的网络数据包/秒
        - 传出的网络数据包/秒
        - 传入的网络 KB/秒
        - 传出的网络 KB/秒
1.  完成数据迁移后，执行与企业所有者协定的所有验证测试。 仅当获得初始源系统的结果时，才接受验证测试结果。
1.  检查接口是否可以正常工作，并且其他应用程序是否可以与新部署的生产系统进行通信。
1.  通过 SAP 事务 STMS 检查传输和更正系统。
1.  当发布系统用于生产后，执行数据库备份。
1.  当发布系统用于生产后，为 SAP 应用层 VM 执行 VM 备份。
1.  针对之前不属于当前上线阶段但与在此上线阶段迁移到 Azure 的 SAP 系统进行通信的 SAP 系统，需要在 SM51 中重置主机名缓冲区。 此步骤将删除与迁移到 Azure 的应用程序实例的名称关联的旧缓存 IP 地址。  


## <a name="post-production"></a>后期生产阶段
此阶段包括监视、操作和管理系统。 从 SAP 的角度来看，需要执行在旧的托管位置完成的常规任务。 还需要完成以下 Azure 特定任务：

1. 查看高收费系统的 Azure 发票。
2. 优化 VM 端和存储端的性价比。
3. 优化可关闭系统的时间。  


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [SAP NetWeaver 的 Azure 虚拟机规划和实施指南](./planning-guide.md)
- [SAP NetWeaver 的 Azure 虚拟机部署](./deployment-guide.md)
- [部署适用于 SAP 工作负载的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md)