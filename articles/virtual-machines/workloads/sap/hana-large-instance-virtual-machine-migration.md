---
title: 将 Azure 上的 SAP HANA（大型实例）迁移到 Azure 虚拟机上| Microsoft Docs
description: 如何在将 Azure 上的 SAP HANA 迁移 (大型实例) 到 Azure 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2021
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e401ee0a8760033bfada5f054ad4c61904a62a6a
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136842"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 上的大型实例 SAP HANA 到 Azure 虚拟机的迁移
本文介绍可行的 Azure 大型实例部署方案，并提供可尽量减少过渡期间的停机时间的规划和迁移方法。

## <a name="overview"></a>概述
Azure SAP HANA 大型实例 (HLI) 自 2016 年 9 月开始宣布推出。 从那以后，许多客户已经为其内存中计算平台采用了此硬件即服务产品。 近年来，Azure 虚拟机 (VM) 扩展了容量，其 HANA 横向扩展部署支持范围超过了大多数企业客户的 ERP 数据库容量需求。 许多客户对将 SAP HANA 工作负载从物理服务器迁移到 Azure VM 很感兴趣。

本文并非循序渐进的配置文档。 它介绍了常见的部署模型，并提供了规划和迁移建议。 我们的目的是提供必要的注意事项，以尽量减少过渡期间的停机时间。

## <a name="assumptions"></a>假设
本文作出以下假设：
- 我们唯一考虑的是将同构的 HANA 数据库计算服务从 HANA 大型实例 (HLI) 迁移到 Azure VM，而无需进行大量的软件升级或修补。 这些次要的版本更新包括使用较新的操作系统 (OS) 版本，或明确声明受相关 SAP 说明支持的 HANA 版本。 
- 所有更新/升级活动将在迁移前后完成。  例如，将 SAP HANA MCOS 转换为 MDC 部署。 
- 停机时间最短的迁移方法是使用 SAP HANA 系统复制。 其他迁移方法不属于本文档的讨论范围。
- 本指南适用于 Rev3 和 HLI Rev4 SKU。
- 迁移过程中，HANA 部署体系结构基本保持不变。  也就是说，具有单实例灾难恢复 (DR) 的系统在目标位置将保持不变。
- 你已查看并理解目标体系结构的服务级别协议 (SLA)。 
- HLI 和 VM 之间的商业术语有所不同。 请监视 VM 的使用情况，以管理成本。
- 你了解 HLI 是专用计算平台，而 VM 在共享但独立的基础结构上运行。
- 你已验证目标 VM 支持预期的体系结构。 有关所有受 SAP HANA 部署支持的认证 VM SKU 列表，请参阅 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 你已验证规划和迁移计划。
- 灾难恢复 VM 计划以及主站点。  迁移后，无法使用 HLI 作为 VM 上运行的主站点的 DR 节点。
- 你已根据企业可恢复性和合规性要求将所需的备份文件复制到目标 VM。 利用 VM 辅助备份功能，可以在转换期间进行时点恢复。
- 为实现 SAP HANA 系统复制 (HSR) 高可用性 (HA)，需要按照适用于 [SLES](./high-availability-guide-suse-pacemaker.md) 和 [RHEL](./high-availability-guide-rhel-pacemaker.md) 的 SAP HANA HA 指南设置并配置 STONITH 设备。  它不像 HLI 案例那样预先配置。
- 此迁移方法不适用携带 Optane 配置的 HLI SKU。

## <a name="deployment-scenarios"></a>部署方案
对于所有 HLI 方案，都可以迁移到 Azure VM。 下表汇总了 HLI 的常用部署模型。 若要利用补充性 Azure 服务，可能需要进行少量的体系结构更改。

| 方案 ID | HLI 方案 | 完全迁移到 VM？ | 备注 |
| --- | --- | --- | --- |
| 1 | [具有一个 SID 的单节点](./hana-supported-scenario.md#single-node-with-one-sid) | 是 | - |
| 2 | [使用“在一个系统中包含多个组件”(MCOS) 的单个节点](./hana-supported-scenario.md#single-node-mcos) | 是 | - |
| 3 | [使用存储复制进行 DR 的单节点](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | 否 | 存储复制在 Azure 虚拟平台中不可用；将当前 DR 解决方案更改为 HSR 或备份/还原。 |
| 4 | [使用存储复制进行 DR（多用途）的单节点](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | 否 | 存储复制在 Azure 虚拟平台中不可用；将当前 DR 解决方案更改为 HSR 或备份/还原。 |
| 5 | [带有 STONITH 的高可用性 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | 是 | 目标 VM 没有预先配置的 SBD。  选择并部署 STONITH 解决方案。  可能的选项：Azure 隔离代理（在 [RHEL](./high-availability-guide-rhel-pacemaker.md) 和 [SLES](./high-availability-guide-suse-pacemaker.md) 中均受支持）和STONITH 块设备 (SBD)。 |
| 6 | [具有 HSR 的 HA，带有存储复制的 DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 替换存储复制，以使用 HSR 或备份/还原解决 DR 需求。 |
| 7 | [主机自动故障转移 (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | 是 | 使用 Azure NetApp 文件 (ANF) 作为 Azure VM 中的共享存储。 |
| 8 | [使用备用节点的横向扩展](./hana-supported-scenario.md#scale-out-with-standby) | 是 | 包含 M128s、M416s、M416ms VM 的 BW/4HANA，ANF 仅用于存储。 |
| 9 | [不使用备用节点的横向扩展](./hana-supported-scenario.md#scale-out-without-standby) | 是 | 包含 M128s、M416s、M416ms VM 的 BW/4HANA（使用或不使用 ANF 作为存储）。 |
| 10 | [使用存储复制进行 DR 横向扩展](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | 否 | 替换存储复制，以使用 HSR 或备份/还原解决 DR 需求。 |
| 11 | [使用 HSR 进行 DR 的单节点](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [单节点 HSR 到 DR（成本优化）](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [具有 HSR 的 HA 和 DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [具有 HSR 的 HA 和 DR（成本优化）](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 进行 DR 横向扩展](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | 是 | 带 M128s 的 BW/4HANA。 M416s、M416ms VM（使用或不使用 ANF 作为存储）。 |


## <a name="source-hli-planning"></a>源 (HLI) 规划
在加入 HLI 服务器时，你和 Microsoft 服务管理已完成对计算、网络、存储和 OS 特定设置的规划，以便运行 SAP HANA 数据库。 迁移到 Azure VM 需要进行类似的规划。

### <a name="sap-hana-housekeeping"></a>SAP HANA 保养工作 
整理数据库内容，保证不需要的、过时的数据或陈旧的日志不会迁移到新数据库，这是一个非常好的实操做法。  保养工作通常包括删除或存档旧的、过期的或非活动的数据。  应在非生产系统中测试此“数据清理”操作，以在生产环境中使用之前验证其数据剪裁有效性。

### <a name="allow-network-connectivity-for-new-vms-and-virtual-network"></a>允许新 VM 和虚拟网络的网络连接 
在 HLI 部署中，已根据 [SAP HANA（大型实例）网络体系结构](./hana-network-architecture.md)中所述的信息设置了网络。 此外，已根据 [Azure 中的路由](./hana-network-architecture.md#routing-in-azure)部分中所述的方式完成网络流量路由。
- 新 VM 迁移目标是否已放置在已允许其 IP 地址范围连接到 HLI 的现有虚拟网络中？ 如果是，则不需要执行进一步的连接更新。
- 新 Azure VM 是否已放置在新的 Microsoft Azure 虚拟网络中（也许在另一区域），并与现有虚拟网络建立了对等互连？ 如果是，则可以使用 ExpressRoute 服务密钥和原始 HLI 预配中的资源 ID 来允许此新虚拟网络 IP 范围进行访问。 与 Microsoft 服务管理协调，使虚拟网络能够连接 HLI。  
    > [!NOTE]
    > 为了最大程度地减少应用程序层和数据库层之间的网络延迟，应用程序层和数据库层必须位于同一虚拟网络上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>现有应用层可用性集、可用性区域和邻近放置组 (PPG)
我们已设计当前部署模型来满足特定的服务级别目标。 在进行这种迁移的过程中，请确保目标基础结构能够满足甚至超出规定的目标。  
SAP 应用程序服务器有可能放置在可用性集中。 如果当前部署服务级别令人满意，并且目标 VM 采用了 HLI 逻辑名称的主机名，则无需更新任何 SAP 配置文件即可更新指向 VM IP 的域名服务 (DNS) 地址解析。
- 如果未使用 PPG，请确保将所有应用程序和数据库服务器放置在同一区域，以最大程度地减少网络延迟。
- 如果使用了 PPG，请参阅本文稍后的[可用性集、可用性区域和邻近放置组](#availability-sets-availability-zones-and-proximity-placement-groups)部分。

### <a name="storage-replication-discontinuance-process-if-used"></a>存储复制中止进程（若已使用）
如果将存储复制用作 DR 解决方案，请在 SAP 应用程序关闭后终止存储复制。 在此之前，请确保最后的 SAP HANA 目录、日志文件和数据备份已复制到远程 DR HLI 存储卷。 如果在从物理服务器过渡到 Azure VM 期间发生灾难，则这种复制就非常重要。

### <a name="data-backups-preservation-consideration"></a>数据备份保留注意事项
过渡到 Azure VM 上的 SAP HANA 后，将无法轻松访问 HLI 上的基于快照的数据和日志备份，或者无法轻松将其还原到 VM。 建议在切换之前的几周内在 HLI 上创建文件级备份和快照。 将这些备份文件复制到 Azure 存储帐户，可通过新的 SAP HANA VM 访问。 另外，在早期过渡期间，在基于 Azure 的备份生成足够的历史记录来满足时间点恢复要求之前，请创建文件级备份。 

备份 HLI 内容至关重要。 另外，明智的做法是创建随时可供访问的完整 SAP 环境备份，以便在需要回滚时使用。

### <a name="adjusting-system-monitoring"></a>调整系统监视 
可以使用多种不同的工具来监视 SAP 环境中的系统并对其发送警报通知。 请记得采取适当的措施来整合有关监视的更改，并根据需要更新警报通知收件人。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 操作团队参与 
基于现有的 HLI 实例，打开 Azure 门户的票证。  创建支持票证后，支持工程师将通过电子邮件与你联系。  

### <a name="engage-microsoft-account-team"></a>参与 Microsoft 帐户团队
将迁移安排在接近 HLI 合同的周年续签时间，以最大程度地减少计算资源的不必要支出。 若要解除 HLI，请与客户团队协调合同终止和单元关闭事宜。

## <a name="destination-planning"></a>目标规划
若要部署新的基础结构来取代现有基础结构，精心的规划非常重要。 确保新添置的设备能够满足更大方案的需求。 下面是需要注意的一些要点。

### <a name="resource-availability-in-the-target-region"></a>目标区域的资源可用性 
当前的 SAP 应用程序服务器部署区域通常与关联的 HLI 非常靠近。 但是，HLI 的位置比可用的 Azure 区域少。 将物理 HLI 迁移到 Azure VM 时，还可以通过微调所有相关服务的邻近距离来优化性能。  执行此操作时，请确保所选区域包含所有必需的资源。 例如，可能需要检查特定 VM 系列的可用性或 Azure 区域产品/服务的高可用性设置。

### <a name="virtual-network"></a>虚拟网络 
是要在现有虚拟网络中运行新的 HANA 数据库，还是创建新的虚拟网络？ 主要的决定因素是当前 SAP 环境的网络布局。 此外，当基础结构从单区域部署转到双区域部署并使用 PPG 时，会导致发生体系结构更改。 有关详细信息，请参阅[Azure PPG 如何通过 SAP 应用程序实现网络延迟最小化](./sap-proximity-placement-scenarios.md)。   

### <a name="security"></a>安全性
无论新 SAP HANA VM 是在新的还是现有的 VNet /子网上运行，它都是对你的业务至关重要的新服务。 有必要为它提供保护。 确保访问控制符合公司的安全策略。

### <a name="vm-sizing-recommendation"></a>VM 规模建议
此迁移也是正确调整 HANA 计算引擎规模的机会。 可以结合使用 HANA [系统视图](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)和 HANA Studio 来了解系统资源消耗，使规模适当以提高支出效率。

### <a name="storage"></a>存储 
存储性能是影响 SAP 应用程序用户体验的因素之一。 为给定的 VM SKU 发布了最小存储布局。 有关详细信息，请参阅 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)。 建议查看这些规格，并与现有的 HLI 系统统计信息进行比较，以确保新 HANA VM 的 IO 容量和性能足够。

是否要为新 HANA VM 及其关联的服务器配置 PPG？ 如果是，请提交支持票证以检查并确保存储和 VM 的共同位置。 由于备份解决方案可能需要更改，因此还应重新评估存储成本，以免运营开支出现意外。

### <a name="storage-replication-for-disaster-recovery"></a>用于灾难恢复的存储复制
使用 HLI 时，存储复制是灾难恢复的默认选项。 此功能不是 Azure VM 上的 SAP HANA 的默认选项。 考虑满足业务需求的 HSR、备份/还原或其他受支持解决方案。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性集、可用性区域和邻近放置组 
可以缩短应用程序层与 SAP HANA 之间的距离以尽量减少网络延迟。 将新数据库 VM 和当前 SAP 应用程序服务器置于 PPG 中。 有关 Azure 可用性集和可用性区域如何与 SAP 部署的 PPG 配合工作的信息，请参阅[邻近放置组](./sap-proximity-placement-scenarios.md)。

如果 HANA 系统的成员已部署在多个 Azure 区域中，则你应该知道所选区域的延迟趋势。 合理放置 SAP 系统组件，以缩短 SAP 应用程序与数据库之间的距离。 利用公共域[可用性区域延迟测试工具](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)，让测量更轻松。  


### <a name="backup-strategy"></a>备份策略
我们的许多客户已经对 HLI 上的 SAP HANA 使用了第三方备份解决方案。  如果你也是这样，只需配置添加的受保护 VM 和 HANA 数据库。 如果迁移后正在解除计算机，可以取消计划进行中的 HLI 备份作业。

适用于 VM 上的 SAP HANA 的 Azure 备份现已推出正式版。  有关 Azure VM 中的 SAP HANA 备份的详细信息，请参阅[备份](../../../backup/backup-azure-sap-hana-database.md)、[还原](../../../backup/sap-hana-db-restore.md)和[管理](../../../backup/sap-hana-db-manage.md)。

### <a name="dr-strategy"></a>DR 策略
如果服务级别目标能够容许较长的恢复时间，则备份可能非常轻松。 最简单经济的 DR 策略是备份到 Blob 存储，并就地还原或还原到新 VM。
 
在大型实例平台上，HANA DR 通常是通过 HSR 完成的。 在 Azure VM 上，HSR 也是最自然的本机 SAP HANA DR 解决方案。 无论源部署是单实例部署还是群集部署，都需要在 DR 区域中提供源基础结构的副本。
在主 HLI 迁移到 VM 后，将配置此 DR 副本。  DR HANA DB 将作为辅助复制站点注册到 VM 实例上的主 SAP HANA。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 应用程序服务器连接目标更改
HSR 迁移会生成新的 HANA 数据库主机，同时还会生成应用程序层的新数据库主机名。 请修改 SAP 配置文件以反映新主机名。 如果切换是通过名称解析来保留主机名，则不需要更改配置文件。

### <a name="operating-system-os"></a>操作系统 (OS)
用于 HLI 和 VM 的 OS 映像虽然处于相同的发布级别（例如 SLES 12 SP4），但两者是不同的。 请在 HLI 上验证所需的包、修补程序、补丁、内核和安全修复。 然后在目标上安装相同的包。  可以使用 HSR 从较旧的 OS 复制到采用较新 OS 版本的 VM。  查看 [SAP 说明 2763388](https://launchpad.support.sap.com/#/notes/2763388) 来确定受支持的版本。

### <a name="new-sap-license-request"></a>新 SAP 的许可证请求
新的 HANA 系统请求新 SAP 许可证的简单调用现在已迁移到 VM。

### <a name="service-level-agreement-sla-differences"></a>服务级别协议 (SLA) 差异
作者喜欢指出 HLI 和 Azure 虚拟机之间的可用性 SLA 差异。  例如，群集 HLI HA 对可用性为 99.99%。 若要实现相同的 SLA，需要在可用性区域中部署 VM。 [虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 介绍了各种 VM 配置的可用性，使客户能够规划其目标基础结构。


## <a name="migration-strategy"></a>迁移策略
在本文档中，我们只介绍 HANA 系统从 HLI 迁移到 Azure VM 的复制方法。  根据部署的目标存储解决方案，此过程会稍有不同。 高级步骤如下所述。

### <a name="vm-with-premiumultra-disks-for-data"></a>具有高级/超磁盘数据的 VM
对于部署了高级或超级磁盘的 VM，标准 SAP HANA 系统复制配置适用于 HSR 设置。 有关设置系统复制的步骤概述，请参阅 [SAP 帮助文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)。 该文章还介绍了如何接管辅助系统、故障回复到主系统，以及禁用系统复制。 对于迁移，我们只需设置、接管和禁用复制步骤。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>具有数据和日志卷 ANF 的 VM
概括而言，需要将完整数据和日志卷的最新 HLI 存储快照复制到 Azure 存储。 目标 HANA VM 可以在该位置访问和恢复这些快照。  任何本机 Linux 复制工具都可以完成复制进程。  

> [!IMPORTANT]
> 复制和数据传输可能需要几个小时，具体取决于 HANA 数据库的大小和网络带宽。 批量复制过程应在主 HANA 数据库关闭之前完成。

### <a name="mcos-to-mdc-conversion"></a>MCOS 到 MDC 的转换
我们的一些 HLI 客户已经使用了“一个系统 (MCOS)”部署模型的多个组件。 因为可以绕过更早的 SAP HANA 版本对多数据库容器 (MDC) 存储快照的限制。  在 MCOS 模型中，多个独立的 SAP HANA 实例堆叠在一个 HANA 大型实例上。 使用 HSR 可以正常迁移，但会生成多个 HANA VM，每个 VM 各有一个租户数据库。 此模型会导致环境的繁忙程度超出你的忍受范围。 SAP HANA 2.0 的默认部署为 MDC。 一种替代方法是在完成 HSR 迁移后执行 [HANA 租户移动](https://launchpad.support.sap.com/#/notes/2472478)。 HANA 租户移动会将这些独立的 HANA 数据库合并到单个 HANA 容器中的共同租户中。  

### <a name="application-layer-consideration"></a>应用程序层注意事项
数据库服务器被视为 SAP 系统的中心。  应将所有应用程序服务器放置在靠近 SAP HANA 数据库的位置。 在某些情况下，若要使用新的 PPG，可能需要将现有应用程序服务器移到 HANA VM 所在的 PPG 上。  如果你的部署模板已准备就绪，则构建新的应用程序服务器可能会更容易。

以最佳方式放置现有应用程序服务器和新的 HANA VM。 这样，除非需要更大的容量，否则无需构建新的应用程序服务器。

当你构建新的基础结构以增强服务可用性时，现有应用程序服务器可能会变得不必要。 可将其关闭并删除。
如果目标 VM 主机名已更改，因此不同于 HLI 主机名，请调整 SAP 应用程序服务器配置文件，使之指向新的主机。 如果只有 HANA 数据库 IP 地址发生更改，请更新 DNS 记录，使传入连接指向新的 HANA VM。

### <a name="acceptance-test"></a>验收测试
与异构迁移相比，从 HLI 到 VM 的迁移不会对数据库内容进行实质性的更改。 但我们仍建议检查新设置的性能。  

### <a name="cutover-plan"></a>切换计划
尽管这种迁移是直接的，但确实涉及到解除现有数据库。  如果需要回退，仔细规划保留源系统及其内容和备份映像至关重要。 良好的规划确实会让逆转更快。   

## <a name="post-migration"></a>迁移之后
只有在我们安全分离了任何与 HLI 相关的服务和连接以确保数据完整性之后，迁移作业才算完成。 此外，我们建议关闭不必要的服务。 本部分列出了几个比较重要的事项。

### <a name="decommissioning-the-hli"></a>停用 HLI
成功将 HANA 数据库迁移到 Azure VM 后，请确保 HLI 数据库上没有运行业务事务。  不过，根据需要使 HLI 保持运行与其本地备份保留时段相同的时间可确保加快恢复速度。 仅当本地备份保留时段已过后，才解除 HANA 大型实例。 然后联系 Microsoft 代表，终结为 Microsoft 做出的契约性 HLI 承诺。

### <a name="remove-any-proxy-for-example-iptables-bigip-configured-for-hli"></a>删除为 HLI 配置的任何代理（例如 Iptables、BIGIP） 
如果使用了 IPTables 之类的代理服务来与 HLI 来回路由本地流量，在成功迁移到 VM 后，不再需要该服务。  尽管如此，只要 HLI 处于待命状态，就应保留此连接服务。  请仅在完全解除 HLI 之后才关闭该服务。

### <a name="remove-global-reach-for-hli"></a>删除 HLI 的 Global Reach 
Global Reach 用于连接客户的 ExpressRoute 网关和 HLI 的 ExpressRoute 网关。  允许客户的本地流量直接访问 HLI 租户，而无需使用代理服务。 迁移后，如果没有 HLI 单元，就不再需要此连接。 与 IPTables 代理服务一样，在完全解除 HLI 之前，也应该保留 GlobalReach。

### <a name="operating-system-subscription--movereuse"></a>操作系统订阅 — 移动/重复使用
由于已部署 VM 服务器并解除 HLI，因此可以替换或重复使用 OS 订阅。 无需重复支付 OS 许可证费用。

## <a name="next-steps"></a>后续步骤

规划 SAP 部署。

> [!div class="nextstepaction"]
> [Azure 上的 SAP 工作负载：规划和部署清单](sap-deployment-checklist.md)
