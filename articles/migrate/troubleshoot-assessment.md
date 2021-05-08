---
title: Azure Migrate 中的评估和依赖项可视化的故障排查
description: 获取关于 Azure Migrate 中的评估和依赖项可视化的帮助。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863592"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>排查评估/依赖项可视化问题

本文将帮助你排查与 [Azure Migrate：发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)相关的评估和依赖项可视化问题。


## <a name="assessment-readiness-issues"></a>评估就绪性问题

解决评估就绪性问题，如下所示：

**问题** | **修补程序**
--- | ---
引导类型不受支持 | Azure 不支持引导类型为 EFI 的 VM。 建议在运行迁移之前将引导类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来处理此类 VM 的迁移。 在迁移过程中，它会将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows 操作系统 | 这些操作系统的支持日期已结束，需要自定义支持协议 (CSA) 以获取[Azure 支持](/troubleshoot/azure/virtual-machines/server-software-support)。 请考虑在迁移到 Azure 之前升级。 查看有关[准备运行 Windows Server 2003 的服务器](prepare-windows-server-2003-migration.md) 以迁移到 Azure 的信息。
不支持的 Windows 操作系统 | Azure 仅支持[所选的 Windows 操作系统版本](/troubleshoot/azure/virtual-machines/server-software-support)。 请考虑在迁移到 Azure 之前升级。
有条件认可的 Linux OS | Azure 仅认可[所选的 LINUX 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级。 请参阅[此处](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment)了解详细信息。
未经认可的 Linux OS | 服务器可以在 Azure 中启动，但 Azure 不提供任何操作系统支持。 在迁移到 Azure 之前，请考虑升级到[认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
未知操作系统 | 在 vCenter Server 中，将 VM 的操作系统指定为“其他”。 此行为会阻止 Azure Migrate 验证 VM 的 Azure 迁移就绪性。 在迁移服务器之前，请确保 Azur [支持](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements)该操作系统。
不支持的位版本 | 具有32位操作系统的 VM 可能会在 Azure 中启动，但建议在迁移到 Azure 之前升级到64位。
需要 Microsoft Visual Studio 订阅 | 服务器正在运行仅通过 Visual Studio 订阅支持的 Windows 客户端操作系统。
未找到所需存储性能的 VM | 服务器所需的存储性能（每秒输入/输出操作数 [IOPS] 和吞吐量）超出了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
未找到所需网络性能的 VM | 服务器所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少服务器的网络需求。
未在指定位置找到 VM | 在迁移之前使用不同目标位置。
存在一个或多个不合适的磁盘 | 附加到 VM 的一个或多个磁盘不符合 Azure 要求。<br/><br/> Azure Migrate：发现和评估目前不支持超级 SSD 磁盘，并基于高级托管磁盘 (32 TB) 的磁盘限制来评估磁盘。<br/><br/> 对于附加到 VM 的每个磁盘，请确保磁盘大小 < 64 TB （由超级 SSD 磁盘支持）。<br/><br/> 如果不是这样，请在迁移到 Azure 之前减小磁盘大小，或者在 Azure 中使用多个磁盘并[将它们捆绑到一起](../virtual-machines/premium-storage-performance.md#disk-striping)，以获得更高的存储限制。 请确保 Azure [托管虚拟机磁盘](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)支持每个磁盘所需的性能（IOPS/吞吐量）。
存在一个或多个不合适网络适配器。 | 在迁移之前从服务器中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从服务器中删除未使用的磁盘。
磁盘大小超过限制 | Azure Migrate：发现和评估目前不支持超级 SSD 磁盘，并基于高级磁盘限制 (32 TB) 来评估磁盘。<br/><br/> 但是，Azure 支持的磁盘大小最高可达 64 TB（由超级 SSD 磁盘支持）。 在迁移之前将磁盘缩减为小于 64 TB，或在 Azure 中使用多个磁盘并[将它们捆绑到一起](../virtual-machines/premium-storage-performance.md#disk-striping)，以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误而无法确定磁盘适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 无法找到合适的 VM 类型。 在迁移之前请减少本地服务器的内存和内核数。
由于内部错误而无法确定 VM 适用性 | 请尝试为组创建一个新评估。
由于内部错误而无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于内部错误而无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。
找不到产品/服务币种预留实例的 VM 大小 | 服务器标记为“不适用”，因为找不到所选 RI、产品/服务和货币组合的 VM 大小。 编辑评估属性以选择有效的组合，并重新计算评估。 
Internet 协议有条件就绪 | 仅适用于 Azure VMware 解决方案 (AVS) 评估。 AVS 不支持 IPv6 Internet 地址因子。 如果检测到你的服务器具有 IPv6，联系 AVS 团队以获得修正指导。

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>在基于导入的 AVS 评估中的建议迁移工具标记为未知

对于通过 CSV 文件导入的服务器，AVS 评估中的默认的迁移工具是未知的。 但对于 VMware 环境中的服务器，建议使用 VMWare 混合云扩展 (HCX) 解决方案。 [了解详细信息](../azure-vmware/tutorial-deploy-vmware-hcx.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM 在 Azure VM 评估中“有条件就绪”

对于 VMware 和 Hyper-V VM，Azure VM 评估将 Linux VM 标记为“有条件地就绪”，因为存在已知的差距。 

- 该差距使其无法检测本地 VM 上安装的 Linux 操作系统的次版本。
- 例如，对于 RHEL 6.10，当前 Azure VM 评估只检测作为操作系统版本的 RHEL 6。 这是因为 vCenter Server 和 Hyper-V 主机不提供 Linux VM 操作系统的内核版本。
-  由于 Azure 仅支持特定版本的 Linux，因此 Linux VM 目前在 Azure VM 评估中标记为“有条件就绪”。
- 同时，可以通过查看 [Azure Linux 支持](../virtual-machines/linux/endorsed-distros.md)来确定在本地 VM 上运行的 Linux OS 是否 在 Azure 中经过认可。
-  验证了认可的分发后，可以忽略此警告。

可通过在 VMware VM 上启用[应用程序发现](./how-to-discover-applications.md)来解决这种差距。 Azure VM 评估通过提供的来宾凭据来使用从 VM 中检测到的操作系统。 在 Windows 和 Linux VM 情况下，此操作系统数据标识正确的操作系统信息。

## <a name="operating-system-version-not-available"></a>操作系统版本不可用

对于物理服务器，操作系统次版本信息应该可用。 如果不可用，请联系 Microsoft 支持部门。 对于 VMware 环境中的服务器，Azure Migrate 使用 vCenter Server 中为 VM 指定的操作系统信息。 但 vCenter Server 不提供适操作系统的次版本。 若要发现次版本，需要设置[应用程序发现](./how-to-discover-applications.md)。 对于 Hyper-V VM，不支持操作系统次版本发现。 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>在 Azure VM 评估中Azure SKU 大于本地

Azure VM 评估可能会建议 Azure VM SKU 具有比当前本地分配更多的内核和内存，具体取决于评估类型：

- VM SKU 建议取决于评估属性。
- 这受您在 Azure VM 评估中执行的评估类型的影响：“基于性能”或“根据本地”。 
- 对于基于性能的评估，Azure VM 评估考虑本地 VM 的利用率数据（CPU、内存、磁盘和网络利用率），以确定适合本地 VM 的目标 VM SKU。 在确定有效利用率时，它还添加了舒适因子。
- 对于本地大小调整，不考虑性能数据，并根据本地分配推荐目标 SKU。

为了说明这会如何影响建议，我们举个例子：

现在有具有 4 个内核和 8 GB 内存的本地 VM，CPU 利用率为 50% 个并且内存利用率为 50%，指定的舒适因子为 1.3.

-  如果评估是“根据本地”，则建议使用具有 4 个内核和 8 GB 内存的 Azure VM SKU。
- 如果评估是基于性能，则根据有效的 CPU 和内存利用率（4 个内核的 50% * 1.3 = 2.6 个内核和 8 GB 内存的 50% * 1.3 = 5.3 GB 内存），建议使用具有 4 个内核（最接近的受支持内核数）和 8 GB 内存大小（最接近的受支持的内存大小）的最便宜的 VM SKU。
- [详细了解](concepts-assessment-calculation.md#types-of-assessments)评估调整大小。

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>为什么在 Azure VM 评估中建议的 Azure 磁盘 SKU 比本地更大？

Azure VM 评估可能会根据评估类型建议更大的磁盘。
- Azure 的磁盘大小调整取决于两个评估属性：大小调整标准和存储类型。
- 如果大小调整标准是“基于性能”且存储类型设置为“自动”，则当标识目标磁盘类型（标准 HDD、标准 SSD 或高级磁盘）时会考虑磁盘的 IOPS 和吞吐量值。  建议使用磁盘类型中的磁盘 SKU，并且该建议考虑到本地磁盘的大小要求。
- 如果大小调整条件是“基于性能”且存储类型为“高级”，则建议根据本地磁盘的 IOPS、吞吐量和大小要求在 Azure 中使用高级磁盘 SKU。  当大小调整标准为“根据本地”且存储类型为“标准 HDD”、“标准 SSD”或“高级”时，使用相同的逻辑进行磁盘大小调整。   

例如，如果具有 32 GB 内存的本地磁盘，但是总的读写 IOPS 为 800 IOPS，则 Azure VM 评估将建议使用高级磁盘（由于 IOPS 要求较高），然后建议使用可以支持所需 IOPS 和大小的磁盘 SKU。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 因此，即使本地磁盘所需的大小为 32 GB，Azure VM 评估也建议使用更大的磁盘，因为本地磁盘的 IOPS 要求很高。

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>为什么评估报告中的某些/所有 VM 缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地 VM 的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请检查：

- 是否在创建评估期间启动了 VM
- 如果只有内存计数器缺失，而你尝试评估 Hyper-V VM，则请检查是否在这些 VM 上启用了动态内存。 目前有一个已知问题，由于该问题的存在，Azure Migrate 设备无法收集此类 VM 的内存利用率。
- 如果所有性能计数器都丢失，请确保满足评估的端口访问要求。 详细了解 [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、[Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) 和[物理](./migrate-support-matrix-physical.md#port-access)评估的端口访问要求。
注意： 如果缺少任何性能计数器，则 Azure Migrate：发现和评估会回退到本地分配的内核/内存，并相应地建议 VM 大小。

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>为什么 Azure VM 和/或 AVS 评估报告中有些/所有服务器缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地服务器的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请检查：

- 是否在创建评估期间启动了服务器
- 是否只缺少内存计数器，并且你正尝试在 Hyper-V 环境中评估服务器。 在这种情况下，请在服务器上启用动态内存，并“重新计算”评估以反映最新的更改。 仅当服务器启用了动态内存时，设备才可以在 Hyper-V 环境中收集服务器的内存使用率值。

- 如果所有性能计数器均丢失，确保允许端口 443 (HTTPS) 上的出站连接。

    > [!Note]
    > 如果丢失任何性能计数器，则 Azure Migrate：发现和评估会回退到本地分配的内核/内存，并相应地建议 VM 大小。


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>为什么我的 Azure SQL 评估中的部分/全部 SQL 实例/数据库缺少性能数据？

若要确保会收集性能数据，请检查：

- 是否在创建评估期间启动了 SQL Server
- Azure Migrate 中 SQL 代理的连接状态是否为“已连接”，并检查上一个检测信号 
- “已发现的 SQL 实例”边栏选项卡中所有 SQL 实例的 Azure Migrate 连接状态是否均为“已连接”
- 所有性能计数器是否均丢失；确保允许端口 443 (HTTPS) 上的出站连接

如果丢失任何性能计数器，Azure SQL 评估会为该实例/数据库推荐最小的 Azure SQL 配置。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>为什么我的评估的置信度分级较低？

根据计算评估所需的[可用数据点](./concepts-assessment-calculation.md#ratings)的百分比，为“基于性能”的评估计算置信度评级。 下面是为什么评估可能会获得较低置信度分级的原因：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果创建性能持续时间设置为一周的评估，则在对所有数据点启用发现之后，需要等待至少一周才能收集。 如果无法等待这么久，请将性能持续时间缩短，并“重新计算”评估。
 
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得较高的置信度分级，请确保： 
    - 服务器在评估期间处于开机状态
    - 允许端口 443 上的出站连接
    - 为 Hyper-V 服务器启用了动态内存 
    - Azure Migrate 中代理的连接状态为“已连接”，并请检查上一个检测信号
    - 对于 Azure SQL 评估，“已发现的 SQL 实例”边栏选项卡中所有 SQL 实例的 Azure Migrate 连接状态均为“已连接”

    请重新计算评估以反映置信度评级的最新更改。

- 对于 Azure VM 和 AVS 评估，启动发现后，创建了几乎很少的服务器。 例如，如果针对最后一个月的性能历史记录创建评估，但仅仅在一周前在环境中创建了几乎很少的服务器。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且置信度分级会较低。 [了解详细信息](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- 对于 Azure SQL 评估，很少有 SQL 实例或数据库是在发现开始后创建的。 例如，如果针对最后一个月的性能历史记录创建评估，但仅仅在一周前在环境中创建了几乎很少的 SQL 实例或数据库。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且置信度分级会较低。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 评估中是否包含操作系统许可证？

Azure VM 评估目前只考虑 Windows 服务器的操作系统许可成本。 目前未考虑 Linux 服务器的许可成本。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>基于性能的大小调整如何在 Azure VM 评估中工作？

Azure VM 评估不断收集本地服务器的性能数据，并使用它来对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 [了解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based)收集基于性能的数据。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>为什么我的评估显示一个警告，指出它是通过预留实例、VM 运行时间和折扣 (% ) 的无效组合来创建的？
选择“预留实例”时，“折扣 (%)”和“VM 运行时间”属性不适用。 当你的评估创建时使用了这些属性的无效组合时，编辑按钮和重新计算按钮会被禁用。 请创建新的评估。 [了解详细信息](./concepts-assessment-calculation.md#whats-an-assessment)。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>看不到物理服务器上的某些网络适配器的性能数据

如果物理服务器启用了 Hyper-V 虚拟化就会发生这种情况。 在这些服务器上，由于产品差距，Azure Migrate 当前同时发现物理和虚拟网络适配器。 仅捕获发现的虚拟网络适配器上的网络吞吐量。

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>物理服务器的建议 Azure VM SKU 太大

如果物理服务器启用了 Hyper-V 虚拟化就会发生这种情况。 在这些服务器上，Azure Migrate 当前同时发现物理和虚拟网络适配器。 因此， 发现的网络适配器的数量高于实际值。 由于 Azure VM 评估选择的 Azure VM 可支持所需数量的网络适配器，因此可能会导致 VM 过大。 [了解更多](./concepts-assessment-calculation.md#calculating-sizing)关于 网络适配器的数量对大小调整的影响。 这是未来将解决的产品差距。

## <a name="readiness-category-not-ready-for-my-physical-server"></a>物理服务器的就绪类别为“未就绪”

在启用了 Hyper-V 虚拟化的物理服务器的情况下，就绪类别可能会错误地标记为“未就绪”。 在这些服务器上，由于产品差距，Azure Migrate 当前同时发现物理和虚拟适配器。 因此， 发现的网络适配器的数量高于实际值。 在根据本地和基于性能的评估中，Azure VM 评估会选取可支持所需数量的网络适配器的 Azure VM。 如果发现的网络适配器数量大于 32， 即 Azure VM 上支持的 NIC 的最大数量，则该服务器将标记为“未就绪”。  [了解更多](./concepts-assessment-calculation.md#calculating-sizing)关于 NIC 的数量对大小调整的影响。


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>已发现的 NIC 的数量高于物理服务器的实际 NIC 的数量

如果物理服务器启用了 Hyper-V 虚拟化就会发生这种情况。 在这些服务器上，Azure Migrate 当前同时发现物理和虚拟适配器。 因此， 发现的 NIC 比实际数量高。

## <a name="dependency-visualization-in-azure-government"></a>Azure 政府中的依赖项可视化

Azure 政府不支持基于代理的依赖项分析。 请使用无代理依赖项分析。


## <a name="dependencies-dont-show-after-agent-install"></a>代理安装后不显示依赖项

在本地 VM 上安装了依赖项可视化代理后，Azure Migrate 通常需要 15-30 分钟的时间在门户中显示依赖项。 如果等待超过 30 分钟，请确保 Microsoft Monitoring Agent (MMA) 可以连接到 Log Analytics 工作区。

对于 Windows VM：
1. 在控制面板中，启动 MMA。
2. 在“Microsoft Monitoring Agent 属性” > “Azure Log Analytics (OMS)”中，确保工作区的“状态”为绿色。  
3. 如果状态不是绿色，请尝试删除工作区，然后再次将其添加到 MMA。

    ![MMA 状态](./media/troubleshoot-assessment/mma-properties.png)

对于 Linux VM，请确保 MMA 和依赖项代理的安装命令已成功运行。 请参阅[此处](../azure-monitor/vm/service-map.md#post-installation-issues)的更多故障排除指南。

## <a name="supported-operating-systems"></a>支持的操作系统

- “MMS 代理”：查看支持的 [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 和 [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 操作系统。
- “依赖项代理”：查看支持的 [Windows 和 Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 操作系统。

## <a name="visualize-dependencies-for--1-hour"></a>可视化依赖项持续大于 1 小时

使用无代理依赖项分析时，可以可视化依赖项或将其导出到地图中，持续时间最长为 30 天。

使用基于代理的依赖项分析时，尽管 Azure Migrate 允许返回到上个月的特定的一天，但可视化依赖项的最长持续时间为一小时。 例如，你可以使用依赖项映射中的持续时间功能来查看昨天的依赖项，但只能查看一小时。 但可以使用 Azure Monitor 日志在更长的持续时间内[查询依赖项数据](./how-to-create-group-machine-dependencies.md)。

## <a name="visualized-dependencies-for--10-servers"></a>可视化大于 10 个服务器的依赖项

在 Azure Migrate 中，通过基于代理的依赖项分析，可以[可视化组的依赖项](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)（最多包含 10 个 VM）。 对于更大的组，建议将 VM 拆分为较小的组来可视化依赖项。


## <a name="servers-show-install-agent"></a>服务器显示“安装代理”

将启用了依赖项可视化的服务器迁移到 Azure 后，服务器可能会显示“安装代理”操作，而不是“查看依赖项”，原因如下：

- 迁移到 Azure 后，本地服务器将关闭，并在 Azure 中启动等效 VM。 这些服务器将获得一个不同的 MAC 地址。
- 服务器还可以具有不同的 IP 地址，具体取决于是否保留了本地 IP 地址。
- 如果 MAC 和 IP 地址与本地不同，Azure Migrate 不会将本地服务器与任何服务映射依赖项数据相关联。 在这种情况下，它将显示安装代理的选项，而不是查看依赖项的选项。
- 在测试迁移到 Azure 后，本地服务器仍按预期方式开启。 在 Azure 中启动的等效服务器将获取不同的 MAC 地址，并且可能会获取不同的 IP 地址。 除非阻止来自这些服务器的传出 Azure Monitor 日志流量，否则 Azure Migrate 不会将本地服务器与任何服务映射依赖项数据相关联，因此将显示安装代理的选项，而不是查看依赖项的选项。

## <a name="dependencies-export-csv-shows-unknown-process"></a>依赖项导出 CSV 显示“未知进程”
在无代理依赖项分析中，会尽力地捕获进程名称。 在某些情况下，尽管将捕获源和目标服务器名称以及目标端口，但在依赖项的两端确定进程名称是不可行的。 在这种情况下，该进程将被标记为“未知进程”。

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>尝试在 Azure Migrate 中配置工作区时，未列出我的 Log Analytics 工作区
Azure Migrate 目前支持在“美国东部”、“东南亚”和“西欧”区域创建 OMS 工作区。 即使在 Azure Migrate 外部任何其他区域中创建工作区，目前也不能将它与项目关联。


## <a name="capture-network-traffic"></a>捕获网络流量

收集网络流量日志，如下所示：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 按 F12 键启动开发人员工具。 如果需要，清除“清除导航上的条目”设置。
3. 单击“网络”选项卡，然后开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈指示正在捕获流量。 如果未显示红色圆圈，请选择黑色圆圈来启动。
   - 在 Microsoft Edge 和 Internet Explorer 中，记录应自动启动。 如果未启动，请单击绿色播放按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择“将内容另存为 HAR”。 此操作会将日志压缩并导出为 .har 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中，选择“导出捕获的流量”选项。 此操作会将日志压缩并导出。
6. 选择“控制台”选项卡以查看任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择“另存为”以导出和压缩日志。
   - 在 Microsoft Edge 或 Internet Explorer 中，右键单击错误并选择“全部复制”。
7. 关闭“开发人员工具”。


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>从何处发现我的评估中的操作系统数据？

- 对于 VMware VM，默认情况下，它是 vCenter 提供的操作系统数据。 
   - 对于 VMware linux VM，如果启用了应用程序发现，则从来宾 VM 提取 OS 详细信息。 若要检查是评估中的哪些 OS 详细信息，请前往“发现的服务器”视图，然后将鼠标悬停在“操作系统”列中的值上。 在弹出的文本中，你将能够看到你所看到的 OS 数据是从 vCenter 服务器还是使用 VM 凭据从来宾 VM 中收集的。 
   - 对于 Windows VM，将始终从 vCenter 服务器获取操作系统详细信息。
- 对于 Hyper-V Vm，操作系统数据是从 Hyper-V 主机上收集的
- 对于物理服务器，它是从服务器中提取的。

## <a name="next-steps"></a>后续步骤

[创建](how-to-create-assessment.md)或[自定义](how-to-modify-assessment.md)评估。