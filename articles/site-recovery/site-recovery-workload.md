---
title: 关于使用zure Site Recovery 实现本地应用程序的灾难恢复
description: 介绍可以通过将灾难恢复与 Azure Site Recovery 服务配合使用来保护的工作负荷。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1a5d20e6feacfe72052142c07dc45753b9bc3138
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599105"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>关于本地应用的灾难恢复

本文介绍了可以使用 [Azure Site Recovery](site-recovery-overview.md) 服务进行保护以便进行灾难恢复的本地工作负荷和应用。

## <a name="overview"></a>概述

组织需要制定业务连续性和灾难恢复 (BCDR) 策略来确保工作负荷和数据在计划和非计划停机期间保持安全和可用。 并且，要能够恢复到正常运行状况。

站点恢复就是能够帮助实现 BCDR 策略的一个 Azure 服务。 使用站点恢复，可将应用程序感知的复制部署到云或辅助站点中。 你可以使用 Site Recovery 来管理复制、执行灾难恢复测试，以及运行故障转移和故障回复。 你的应用可以在基于 Windows 或 Linux 的计算机、物理服务器、VMware 或 Hyper-V 上运行。

Site Recovery 与 Microsoft 应用程序集成，例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory。 Microsoft 与 Oracle、SAP 和 Red Hat 等领先供应商密切合作。 可以针对每个应用自定义复制解决方案。

## <a name="why-use-site-recovery-for-application-replication"></a>为何要使用站点恢复来复制应用程序？

站点恢复可帮助实现应用程序级的保护和恢复，如下所述：

- 不区分应用，为受支持计算机上运行的任何工作负荷提供复制。
- 几乎同步的复制，恢复点目标 (RPO) 低至 30 秒，满足大多数关键业务应用的需要。
- 针对单层或多层应用程序的应用一致性快照。
- 与 SQL Server AlwaysOn 集成，并与其他应用程序级复制技术建立伙伴关系。 例如，Active Directory 复制、SQL AlwaysOn 和 Exchange 数据库可用性组 (DAG)。
- 灵活的恢复计划，一次单击即可恢复整个应用程序堆栈，可以在计划中包括外部脚本和手动操作。
- Site Recovery 和 Azure 中的高级网络管理，可以简化应用网络要求。 网络管理，例如，能够保留 IP 地址、配置负载均衡以及与 Azure 流量管理器集成以实现低恢复时间目标 (RTO) 网络切换。
- 丰富的自动化库，提供特定于应用程序的生产就绪型脚本，可以下载并与恢复计划集成。

## <a name="workload-summary"></a>工作负荷摘要

站点恢复可复制受支持计算机上运行的任何应用。 我们还与产品团队合作，对下表中指定的应用进行额外测试。

| **工作负载** |**将 Azure VM 复制到 Azure** |**将 Hyper-V VM 复制到辅助站点** | **将 Hyper-V VM 复制到 Azure** | **将 VMware VM 复制到辅助站点** | **将 VMware VM 复制到 Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |是 |是 |是 |是 |是|
| Web 应用（IIS、SQL） |是 |是 |是 |是 |是|
| System Center Operations Manager |是 |是 |是 |是 |是|
| SharePoint |是 |是 |是 |是 |是|
| SAP<br/><br/>将非群集 SAP 站点复制到 Azure |是（已由 Microsoft 测试） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试）|
| Exchange（非 DAG） |是 |是 |是 |是 |是|
| 远程桌面/VDI |是 |是 |是 |是 |是|
| Linux（操作系统和应用） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试） |是（已由 Microsoft 测试）|
| Dynamics AX |是 |是 |是 |是 |是|
| Windows 文件服务器 |是 |是 |是 |是 |是|
| Citrix XenApp 和 XenDesktop |否|空值 |否 |空值 |否 |

## <a name="replicate-active-directory-and-dns"></a>复制 Active Directory 和 DNS

Active Directory 和 DNS 基础结构对于大多数企业应用而言至关重要。 在灾难恢复过程中，在恢复工作负荷和应用之前，你需要保护和恢复这些基础结构组件。

可以使用站点恢复，为 Active Directory 和 DNS 创建一个完整的自动化灾难恢复计划。 例如，若要将 SharePoint 和 SAP 从主站点故障转移到辅助站点，你可以制定一个首先对 Active Directory 进行故障转移的恢复计划。 然后，使用其他特定于应用的恢复计划对依赖于 Active Directory 的其他应用进行故障转移。

[详细了解](site-recovery-active-directory.md) Active Directory 和 DNS 的灾难恢复。

## <a name="protect-sql-server"></a>保护 SQL Server

SQL Server 为本地数据中心的许多业务应用提供数据服务基础。 Site Recovery 可与 SQL Server HA/DR 技术一起用于保护采用 SQL Server 的多层企业应用。

Site Recovery 提供：

- 为 SQL Server 提供简单且经济高效的灾难恢复解决方案。 将多个版本的 SQL Server 独立服务器和群集复制到 Azure 或辅助站点。
- 集成 SQL AlwaysOn 可用性组，使用 Azure Site Recovery 恢复计划管理故障转移和故障回复。
- 适用于应用程序中所有层（包括 SQL Server 数据库）的端到端恢复计划。
- 使用 Site Recovery 在出现高峰负载时扩展 SQL Server，其方法是让这些负载突发到 Azure 中更大型的 IaaS 虚拟机中。
- 简单的 SQL Server 灾难恢复测试。 可以运行测试故障转移来分析数据，并可以运行合规性检查，且不影响生产环境。

[详细了解](site-recovery-sql.md) SQL Server 的灾难恢复。

## <a name="protect-sharepoint"></a>保护 SharePoint

Azure Site Recovery 可帮助保护 SharePoint 部署，如下所述：

- 消除对用于灾难恢复的备用场的需要以及相关的基础结构成本。 使用 Site Recovery 将整个场（Web 层、应用层和数据库层）复制到 Azure 或辅助站点。
- 简化应用程序部署和管理。 部署到主站点的更新会自动进行复制。 在故障转移和恢复辅助站点中的场之后，这些更新将可供使用。 降低了使备用场保持最新状态的管理复杂性和相关成本。
- 按照副本环境的需要创建一个与生产类似的副本来进行测试和调试，从而简化 SharePoint 应用程序的开发与测试。
- 使用站点恢复将 SharePoint 部署迁移到 Azure，从而简化从本地到云的过渡过程。

[详细了解](site-recovery-sharepoint.md) SharePoint 的灾难恢复。

## <a name="protect-dynamics-ax"></a>保护 Dynamics AX

Azure Site Recovery 可通过以下方式帮助保护 Dynamics AX ERP 解决方案：

- 管理整个 Dynamics AX 环境（Web 和 AOS 层、数据库层、SharePoint）到 Azure 或辅助站点的复制。
- 简化 Dynamics AX 部署到云 (Azure) 的迁移。
- 通过按需创建一个与生产类似的副本来进行测试和调试，简化 Dynamics AX 应用程序的开发与测试。

[详细了解](site-recovery-dynamicsax.md) Dynamic AX 的灾难恢复。

## <a name="protect-remote-desktop-services"></a>保护远程桌面服务

远程桌面服务 (RDS) 启用了虚拟桌面基础结构 (VDI)、基于会话的桌面和应用程序，使用户可以在任何地方工作。

使用 Azure Site Recovery，你可以复制以下服务：

- 将托管或非托管共用虚拟桌面复制到辅助站点。
- 将远程应用程序和会话复制到辅助站点或 Azure。

下表显示了复制选项：

| **RDS** |**将 Azure VM 复制到 Azure** | **将 Hyper-V VM 复制到辅助站点** | **将 Hyper-V VM 复制到 Azure** | **将 VMware VM 复制到辅助站点** | **将 VMware VM 复制到 Azure** | **将物理服务器复制到辅助站点** | **将物理服务器复制到 Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **入池虚拟桌面（非托管）** |否|是 |否 |是 |否 |是 |否 |
| **入池虚拟桌面（托管但不包含 UPD）** |否|是 |否 |是 |否 |是 |否 |
| **远程应用程序和桌面会话（不包含 UPD）** |是|是 |是 |是 |是 |是 |是 |

[详细了解](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) RDS 的灾难恢复。

## <a name="protect-exchange"></a>保护 Exchange

站点恢复可通过以下方式帮助保护 Exchange：

- 对于小型 Exchange 部署，例如单一服务器或服务器单机，站点恢复可以复制和故障转移到 Azure 或辅助站点。
- 对于大型部署，站点恢复可与 Exchange DAGS 集成。
- 在企业中进行 Exchange 灾难恢复时，Exchange DAG 是建议的解决方案。  站点恢复中的恢复计划可以包含 DAG，以便跨站点协调 DAG 故障转移。

若要了解有关 Exchange 灾难恢复的详细信息，请参阅 [Exchange DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups) 和 [Exchange 灾难恢复](/Exchange/high-availability/disaster-recovery/disaster-recovery)。

## <a name="protect-sap"></a>保护 SAP

按如下所述使用站点恢复来保护 SAP 部署：

- 将组件复制到 Azure，以便保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
- 将组件复制到其他 Azure 数据中心，以便保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
- 使用站点恢复将 SAP 部署迁移到 Azure，从而简化云迁移。
- 通过创建一个按需生产克隆来测试 SAP 应用程序，简化 SAP 项目的升级、测试和原型制作。

[详细了解](site-recovery-sap.md) SAP 的灾难恢复。

## <a name="protect-internet-information-services"></a>保护 Internet Information Services

使用 Site Recovery 来保护 Internet Information Services (IIS) 部署，如下所述：

Azure Site Recovery 可以将环境中的关键组件复制到冷远程站点或公有云（例如 Microsoft Azure），从而提供灾难恢复。 由于包含 Web 服务器和数据库的虚拟机将复制到恢复站点，因此不需单独备份配置文件或证书。 依赖于环境变量（在故障转移后已更改）的应用程序映射和绑定可以通过集成到灾难恢复计划中的脚本进行更新。 仅在故障转移期间，才会在恢复站点中启动虚拟机。 Azure Site Recovery 还提供以下功能，帮助你协调端到端故障转移：

- 顺序安排各层中虚拟机的关机和启动。
- 添加脚本，以便在虚拟机启动后更新其上的应用程序依赖项和绑定。 也可使用脚本更新 DNS 服务器，使之指向恢复站点。
- 通过映射主网络和恢复网络，在故障转移前向虚拟机分配 IP 地址，在故障转移后使用不需更新的脚本。
- 能够对多个 Web 应用程序进行一键式故障转移，因此在发生灾难时不会造成混淆。
- 能够在适用于 DR 演练的隔离环境中测试恢复计划。

[详细了解](site-recovery-iis.md) IIS 的灾难恢复。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>保护 Citrix XenApp 和 XenDesktop

从 2020 年 3 月起，Citrix 已宣布弃用并终止对公有云托管工作负载的支持。 因此，不建议使用 Site Recovery 来保护 Citrix 工作负载。

## <a name="next-steps"></a>后续步骤

[详细了解](azure-to-azure-quickstart.md) Azure VM 的灾难恢复。
