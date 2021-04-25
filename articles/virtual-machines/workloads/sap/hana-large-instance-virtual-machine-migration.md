---
title: 将 Azure 上的 SAP HANA（大型实例）迁移到 Azure 虚拟机上| Microsoft Docs
description: 如何在将 Azure 上的 SAP HANA 迁移 (大型实例) 到 Azure 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677044"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 上的大型实例 SAP HANA 到 Azure 虚拟机的迁移
本文介绍可行的 Azure 大型实例部署方案，并提供让转换故障时间最小化的规划和迁移方法

## <a name="overview"></a>概述
自 2016 年 9 月推出了 Azure 大型实例 SAP HANA (HLI)，许多客户已经为其内存中计算平台采用此硬件服务产品。  最近几年来，Azure VM 在 HANA 横向扩展部署的支持下扩展容量，超过了大多数企业客户的 ERP 数据库容量需求。  我们发现客户对将 SAP HANA 工作负荷从物理服务器迁移到 Azure VM 很感兴趣。
本指南不是循序渐进的配置文档。 它介绍了常见的部署模型，并提供了规划和迁移建议。  提供必要的注意事项，目的是最大程度地减少转换故障时间。

## <a name="assumptions"></a>假设
本文作出以下假设：
- 唯一关注的是将 HANA 数据库计算服务从 HANA 大型实例 (HLI) 迁移到 Azure VM，而无需进行大量的软件升级或修补。 这些小版本的更新包括使用最新的操作系统版本，或正面声明受相关 SAP 说明支持的 HANA 版本。 
- 所有更新/升级活动都需要在迁移前后完成。  例如，将 SAP HANA MCOS 转换为 MDC 部署。 
- 最少故障时间的迁移方法是 SAP HANA System Replication。 其他迁移方法不属于本文档的讨论范围。
- 本指南适用于 Rev3 和 HLI Rev4 SKU。
- 迁移过程中，HANA 部署体系结构基本保持不变。  也就是说，具有单实例 DR 的系统在目标位置将保持不变。
- 客户已经查看并了解服务级别协议 (SLA) 的结构目标。 
- HLI 和 VM 之间的商业术语有所不同。 客户应监视其 VM 的使用情况，以管理成本。
- 客户了解 HLI 是专用计算平台而 VM 是在共享但同时独立的基础结构上运行。
- 客户已验证目标 VM 是否支持预期的体系结构。 若要查看所有受 SAP HANA 部署支持的认证 VM SKU，请参阅 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 客户已验证规划和迁移计划。
- 灾难恢复 VM 计划以及主站点。  迁移后，主站点的 DR 节点在 VM 上运行，因此客户无法使用 HLI。
- 客户根据企业可恢复性和合规性要求将所需的备份文件复制到目标 VM。 利用 VM 辅助备份功能，可以在转换期间进行时点恢复。
- 对于 HSR HA，客户需要按照 SAP HANA HA 指南为 [SLES](./high-availability-guide-suse-pacemaker.md) 和 [RHEL](./high-availability-guide-rhel-pacemaker.md) 设置和配置 STONITH 设备。  它不像 HLI 案例那样预先配置。
- 此迁移方法不适用携带 Optane 配置的 HLI SKU。

## <a name="deployment-scenarios"></a>部署方案
下表总结了 HLI 客户的常见部署模型。  适用于任何 HLI 方案迁移到 Azure VM 的方法。  若要利用补充 Azure 服务，可能需要改动少量体系结构。

| 方案 ID | HLI 方案 | 完全迁移到 VM？ | 备注 |
| --- | --- | --- | --- |
| 1 | [具有一个 SID 的单节点](./hana-supported-scenario.md#single-node-with-one-sid) | 是 | - |
| 2 | [具有 MCOS 的单节点](./hana-supported-scenario.md#single-node-mcos) | 是 | - |
| 3 | [使用存储复制进行 DR 的单节点](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | 否 | Azure 虚拟平台无法进行存储复制，将当前 DR 解决方案改为 HSR 或备份/还原 |
| 4 | [使用存储复制进行 DR（多用途）的单节点](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | 否 | Azure 虚拟平台无法进行存储复制，将当前 DR 解决方案改为 HSR 或备份/还原 |
| 5 | [带有 STONITH 的高可用性 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | 是 | 目标 VM 没有预先配置的 SBD。  选择并部署 STONITH 解决方案。  可能的选项：Azure Fencing Agent（同时支持 [RHEL](./high-availability-guide-rhel-pacemaker.md)、[SLES](./high-availability-guide-suse-pacemaker.md)）、SBD |
| 6 | [具有 HSR 的 HA，带有存储复制的 DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 将存储复制替换为 HSR 或备份/还原满足 DR 需要 |
| 7 | [主机自动故障转移 (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | 是 | 使用 ANF 和 Azure VM 共享存储 |
| 8 | [使用备用节点的横向扩展](./hana-supported-scenario.md#scale-out-with-standby) | 是 | 带有 M128s、M416s、M416ms 的 BW/4HANA VM，只使用 ANF 进行存储 |
| 9 | [不使用备用节点的横向扩展](./hana-supported-scenario.md#scale-out-without-standby) | 是 | 带有 M128s、M416s、M416ms 的 BW/4HANA VM（使用或不使用 ANF 进行存储） |
| 10 | [使用存储复制进行 DR 横向扩展](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | 否 | 将存储复制替换为 HSR 或备份/还原满足 DR 需要 |
| 11 | [使用 HSR 进行 DR 的单节点](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [单节点 HSR 到 DR（成本优化）](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [具有 HSR 的 HA 和 DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [具有 HSR 的 HA 和 DR（成本优化）](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 进行 DR 横向扩展](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | 是 | 带 M128s 的 BW/4HANA。 M416s、M416ms VM（使用或不使用 ANF 进行存储） |


## <a name="source-hli-planning"></a>源 (HLI) 规划
在加入 HLI 服务器时，Microsoft 服务管理和客户都要完成对计算、网络、存储和操作系统特定设置的规划，以便运行 SAP HANA 数据库。  迁移到 Azure VM 需要进行类似的规划。

### <a name="sap-hana-housekeeping"></a>SAP HANA 保养工作 
整理数据库内容，保证不需要的、过时的数据或陈旧的日志不会迁移到新数据库，这是一个非常好的实操做法。  保养工作通常包括删除或存档旧的、过期的或非活动的数据。  应在非生产系统中测试这些“数据清理”操作，从而在生产使用之前验证其数据剪裁的有效性。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>允许新 VM 和/或虚拟网络的网络连接 
在客户的 HLI 部署中，已根据 [SAP HANA（大型实例）网络体系结构](./hana-network-architecture.md)中所述的信息设置了网络。 而且，网络流量路由已根据“Azure 路由”一节中概述的方式完成。
- 在将新的 VM 设置为迁移目标时，如果将其放置在现有的虚拟网络中，并且已允许 IP 地址范围连接到 HLI，则不需要进一步的连接更新。
- 如果将新的 Azure VM 放置在新的 Microsoft Azure 虚拟网络中（可能在另一个区域），并与现有的虚拟网络建立对等关系时，可以使用来自原机预配 HLI 的 ExpressRoute 服务密钥和资源 ID 允许此新虚拟网络 IP 范围的访问。  与 Microsoft 服务管理协调，使虚拟网络能够连接 HLI。  注意：若要最大程度地减少应用程序层和数据库层之间的网络延迟，应用程序层和数据库层都必须位于同一虚拟网络上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>现有应用程序层可用性集、可用性区域和邻近放置组 (PPG)
为满足特定的服务级别目标，已完成当前部署模型。  在此操作中，请确保目标基础结构能够满足或超出设定目标。  
更可能的是，将客户 SAP 应用程序服务器放置在可用性集中。  如果当前的部署服务级别令人满意，并且 
- 如果目标 VM 采用了 HLI 逻辑名称的主机名，则无需更新任何 SAP 配置文件即可更新指向 VM IP 的域名服务 (DNS) 地址解析
- 如果未使用 PPG，请确保将所有应用程序和数据库服务器放置在同一区域，以最大程度地减少网络延迟。
- 如果你使用的是 PPG，请参阅本文档的“目标计划、可用性集、可用性区域和邻近放置组 (PPG)”部分。

### <a name="storage-replication-discontinuance-process-if-used"></a>存储复制中止进程（若已使用）
如果将存储复制用作 DR 解决方案，则应在 SAP 应用程序关闭后终止它（非计划）。  此外，最后的 SAP HANA 目录、日志文件和数据备份均已复制到远程 DR HLI 存储卷上。  这样做是为了预防在物理服务器转换到 Azure VM 的过程中发生灾害。

### <a name="data-backups-preservation-consideration"></a>数据备份保留注意事项
切换到 Azure VM 上的 SAP HANA 后，如果需要，则无法轻松访问所有 HLI 上基于快照的数据或日志备份，也无法还原到 VM。 在早期过渡期内，在基于 Azure 的备份生成足够的历史记录以满足时间点恢复要求之前，我们建议在转换几天或几周前，除了 HLI 上的快照，还进行文件备份升级。  将这些备份文件复制到 Azure 存储帐户，可通过新的 SAP HANA VM 访问。
除了备份 HLI 内容外，最好对随时可访问的 SAP 布局进行完整备份，以防需要回滚。

### <a name="adjusting-system-monitoring"></a>调整系统监视 
客户使用多种不同的工具来监视和发送 SAP 环境内系统的警报通知。  该项只是调用适当的操作以纳入用于监视的变更，如果需要可以更新和警报通知收件人。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 操作团队参与 
基于现有的 HLI 实例，打开 Azure 门户的票证。  创建支持票证后，支持工程师将通过电子邮件与你联系。  

### <a name="engage-microsoft-account-team"></a>参与 Microsoft 帐户团队
将迁移计划于 HLI 合同的周年续签时间，可以最大程度地减少计算资源上的不必要支出。 若要停止 HLI 边栏选项卡，需协调合同终止并实际关闭设备。

## <a name="destination-planning"></a>目标规划
建立一个新的基础结构来取代现有的基础结构，理应想到新的添加功能应适用于大型方案。  下面是一些结论要点。

### <a name="resource-availability-in-the-target-region"></a>目标区域的资源可用性 
当前的 SAP 应用程序服务器部署区域通常与关联的 HLI 紧密相邻。  但是，HLI 的位置比可用的 Azure 区域少。  将物理 HLI 迁移到 Azure VM 时，还可以通过“微调”所有相关服务的邻近距离来优化性能。  在执行此操作时，需要考虑的一个重要事项是确保所选区域包含所有必需的资源。  例如，特定 VM 系列或 Azure 区域产品/服务的可用性，以实现高可用性设置。

### <a name="virtual-network"></a>虚拟网络 
客户需要选择是在现有虚拟网络中运行新的 HANA 数据库，还是创建新的虚拟网络。  主要的决定因素是当前 SAP 环境的网络布局。  此外，当基础结构从一个区域部署转到两个区域部署并使用 PPG 时，会使体系结构发生更改。 有关详细信息，请参阅[Azure PPG 如何通过 SAP 应用程序实现网络延迟最小化](./sap-proximity-placement-scenarios.md)。   

### <a name="security"></a>安全性
无论新 SAP HANA VM 是否在新的或现有的 vnet /子网上登录，作为新的业务关键服务都需要保护。  应为此类新的服务评估并部署符合企业信息安全策略的访问控制。

### <a name="vm-sizing-recommendation"></a>VM 规模建议
此迁移也是正确调整 HANA 计算引擎规模的机会。  可以结合使用 HANA [系统视图](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)和 HANA Studio 来了解系统资源消耗，使规模适当以提高支出效率。

### <a name="storage"></a>存储 
存储性能是影响 SAP 应用程序用户体验的因素之一。  基于给定的 VM SKU，最小存储布局发布在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中。 建议查看最低规格，并与现有的 HLI 系统统计信息进行比较，以确保新 HANA VM 的 IO 容量和性能足够。

如果为新 HANA VM 及其关联的服务配置 PPG，请提交支持票证以检查和确保存储和 VM 的共同位置。 由于你的备份解决方案可能需要更改，因此还应再次访问存储成本，以避免操作开支出现意外。

### <a name="storage-replication-for-disaster-recovery"></a>用于灾难恢复的存储复制
借助 HLI，存储复制被作为灾难恢复的默认选项。 此功能不是 Azure VM 上 SAP HANA 的默认选项。 请考虑 HSR、备份/还原或其他受支持的解决方案，满足你的业务需求。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性集、可用性区域和邻近放置组 
若要缩短应用程序层和 SAP HANA 之间的距离以尽量减少网络延迟，请将新的数据库 VM 和当前 SAP 应用程序服务器置于 PPG 中。 请参阅[邻近位置组](./sap-proximity-placement-scenarios.md)，了解 Azure 可用性集和可用性区域如何与 SAP 部署的 PPG 配合使用。
如果将目标 HANA 系统的成员部署在多个 Azure 区域，客户应清楚地了解所选区域的延迟配置文件。 SAP 系统组件的位置对使 SAP 应用程序和数据库之间的距离最近是最佳的。  利用公共域[可用性区域延迟测试工具](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)，让测量更轻松。  


### <a name="backup-strategy"></a>备份策略
许多客户已经对 HLI 上的 SAP HANA 使用第三方备份解决方案。  在这种情况下，只需配置其他受保护的 VM 和 HANA 数据库。  如果迁移后计算机停止工作，现亦可取消进行中的 HLI 备份作业。
VM SAP HANA 上的 Azure 备份现已普遍可用。  有关详细信息，请参阅以下链接：[备份](../../../backup/backup-azure-sap-hana-database.md)、[还原](../../../backup/sap-hana-db-restore.md)、[管理](../../../backup/sap-hana-db-manage.md) Azure VM 中的 SAP HANA 备份。

### <a name="dr-strategy"></a>DR 策略
如果服务级别目标需要更长的恢复时间，则最简单经济的 DR 策略就是简单备份到 blob 存储并就地还原或还原到新的 VM。  
例如，通常通过 HSR 完成 HANA DR 的大型实例平台；在 Azure VM 上，HSR 也是最自然的本机 SAP HANA DR 解决方案。  无论源部署是单实例部署还是群集部署，都需要在 DR 区域提供源基础结构的副本。
在主 HLI 迁移到 VM 后，将配置此 DR 副本。  DR HANA DB 将作为辅助复制站点注册到 VM 实例上的主 SAP HANA。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 应用程序服务器连接目标更改
HSR 迁移会生成新的 HANA DB 主机，从而生成应用程序层的新 HANA DB 主机名，需要修改 SAP 配置文件以反映新的主机名。  如果切换是通过名称解析来保留主机名，则不需要更改配置文件。

### <a name="operating-system"></a>操作系统
尽管处于相同的发布级别，HLI 和 VM 的操作系统映像依然不同，比如 SLES 12 SP4。 客户必须在 HLI 上验证所需的包、热修补程序、补丁、内核和安全修补程序，才能在目标上安装相同的包。  支持使用 HSR 从较旧的操作系统复制到使用较新操作系统版本的 VM 上。  查看 [SAP 说明 2763388](https://launchpad.support.sap.com/#/notes/2763388)，确定特定的受支持版本。

### <a name="new-sap-license-request"></a>新 SAP 的许可证请求
新的 HANA 系统请求新 SAP 许可证的简单调用现在已迁移到 VM。

### <a name="service-level-agreement-sla-differences"></a>服务级别协议 (SLA) 差异
作者喜欢指出 HLI 和 Azure 虚拟机之间的可用性 SLA 差异。  例如，群集 HLI HA 对可用性为 99.99%。 若要获得相同的 SLA，则必须在可用性区域中部署 VM。 [本文](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)介绍了关联部署体系结构的可用性，以便客户可以相应地规划其目标基础结构。


## <a name="migration-strategy"></a>迁移策略
在本文档中，我们只介绍 HANA 系统从 HLI 迁移到 Azure VM 的复制方法。  根据部署的目标存储解决方案，此过程会稍有不同。 高级步骤如下所述。

### <a name="vm-with-premiumultra-disks-for-data"></a>具有高级/超磁盘数据的 VM
对于部署了高级或超磁盘的 VM，标准 SAP HANA 系统复制配置适用于 HSR 设置。  [SAP 帮助文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)概述了设置系统复制、接管辅助系统、故障重回主系统和禁用系统复制所涉及的步骤。  出于迁移目的，我们只需要设置、接管和禁用复制步骤。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>具有数据和日志卷 ANF 的 VM
在高级别中，需要将完整数据和日志卷的最新 HLI 存储快照复制到 Azure 存储中，以供目标 HANA VM 访问和恢复。  任何本机 Linux 复制工具都可以完成复制进程。  

> [!IMPORTANT]
> 复制和数据传输可能需要几个小时，具体取决于 HANA 数据库的规模和网络带宽。  大容量复制进程应在主 HANA DB 停机时间之前完成。

### <a name="mcos-to-mdc-conversion"></a>MCOS 到 MDC 的转换
我们的一些 HLI 客户已经使用了“一个系统 (MCOS)”部署模型的多个组件。  因为可以绕过更早的 SAP HANA 版本对多数据库容器 (MDC) 存储快照的限制。  在 MCOS 模型中，多个独立的 SAP HANA 实例堆积在一个 HLI 边栏选项卡中。  使用 HSR 进行迁移可以正常工作，但会生成多个 HANA VM，每个都携带一个租户数据库。  这种最终状态比客户可能已经习惯的更繁忙。  将 SAP HANA 2.0 默认部署作为 MDC 后，一种可行的替代方法是在 HSR 迁移后执行 [HANA 租户移动](https://launchpad.support.sap.com/#/notes/2472478)。  此进程将这些独立的 HANA 数据库“整合”到单个 HANA 容器中的关联文件中。  

### <a name="application-layer-consideration"></a>应用程序层注意事项
数据库服务器被视为 SAP 系统的中心。  所有应用程序服务器都应置于 SAP HANA DB 附近。  在某些情况下，如果需要重新使用 PPG，可能需要将现有应用程序服务器重新定位到 HANA VM 的 PPG 上。  如果你的部署模板简单易行，则生成新的应用程序服务器可能会更容易。  
如果现有的应用程序服务器和新的 HANA VM 处于最佳位置，则不需要构建新的应用程序服务器，除非需要额外的容量。  
如果为了提高服务可用性构建新的基础结构，则现有应用程序服务器可能会变得不必要，应将其关闭并删除。
如果目标 VM 主机名变化，并且不同于 HLI 主机名，则需要调整 SAP 应用程序服务器配置文件，使之指向新的主机。  如果只更改了 HANA DB 的 IP 地址，则需要进行 DNS 记录更新，以将传入的连接引向新的 HANA VM。

### <a name="acceptance-test"></a>验收测试
尽管从 HLI 到 VM 的迁移与异类迁移相比，不会对数据库内容作任何实质上的更改，我们仍建议验证新设置的主要功能和性能。  

### <a name="cutover-plan"></a>切换计划
虽然这种迁移是直接进行的，但还是需要停用 DB。  如果需要回退，仔细规划保留源系统及其关联的内容和备份映像至关重要。  良好的规划确实会让逆转更快。   


## <a name="post-migration"></a>迁移之后
直到我们安全地分离了任何与 HLI 相关的服务或连接以确保数据完整性，迁移作业才算大功告成。  另外，关闭不必要的服务。  本部分将介绍一些最重要的事项。

### <a name="decommissioning-the-hli"></a>停用 HLI
成功将 HANA DB 迁移到 Azure VM 后，请确保 HLI DB 上没有运行生产业务和事务。  不过，如果需要，保持 HLI 的运行时间和其本地备份保留时段等同是一种安全的做法。  之后再停用 HLI 边栏选项卡。  客户应联系 Microsoft 代表，以合同形式总结和 Microsoft 的 HLI 承诺。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>删除任何代理 HLI 配置（例如：Iptables、BIGIP） 
如果使用类似 IPTables 的代理服务实现本地流量和 HLI 之间的路由，则在成功迁移到 VM 后，不再需要该服务。  但是，只要 HLI 边栏选项卡仍在进行，就应保留此连接服务。  仅在 HLI 边栏选项卡完全停止后关闭服务。

### <a name="remove-global-reach-for-hli"></a>删除 HLI 的 Global Reach 
Global Reach 用于连接客户的 ExpressRoute 网关和 HLI 的 ExpressRoute 网关。  允许客户的本地流量直接访问 HLI 租户，而无需使用代理服务。  迁移后，如果没有 HLI 单元，就不再需要此连接。  与 IPTables 代理服务的情况类似，还应保留 GlobalReach 直到 HLI 边栏选项卡完全停用。

### <a name="operating-system-subscription--movereuse"></a>操作系统订阅 — 移动/重复使用
在建立好 VM 服务器并且停用 HLI 边栏选项卡时，可以替换或重复使用 OS 订阅，以免重复支付 OS 许可证。



## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md)。
- [Azure 上的 SAP 工作负载：规划和部署清单](./sap-deployment-checklist.md)。
