---
title: Azure Migrate 中的排除故障评估
description: 在 Azure Migrate 中获取评估帮助。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.openlocfilehash: d701767f6e8f22b1577b3fb745d01dea00557e8b
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306814"
---
# <a name="troubleshoot-assessment"></a>排查评估问题

本文将帮助你排查与 [Azure Migrate：发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)相关的评估和依赖项可视化问题。

## <a name="assessment-readiness-issues"></a>评估就绪性问题

此表列出了有关修复以下评估就绪问题的帮助。

**问题** | **修补程序**
--- | ---
引导类型不受支持 | Azure 不支持引导类型为 EFI 的 VM。 在运行迁移之前将引导类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来处理此类 VM 的迁移。 在迁移过程中，它会将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows 操作系统 | 这些操作系统的支持日期已结束，需要自定义支持协议以获取 [Azure 支持](/troubleshoot/azure/virtual-machines/server-software-support)。 请考虑在迁移到 Azure 之前升级。 查看有关[准备运行 Windows Server 2003 的服务器](prepare-windows-server-2003-migration.md) 以迁移到 Azure 的信息。
不支持的 Windows 操作系统 | Azure 仅支持[所选的 Windows 操作系统版本](/troubleshoot/azure/virtual-machines/server-software-support)。 请考虑在迁移到 Azure 之前升级。
有条件认可的 Linux OS | Azure 仅认可[所选的 LINUX 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级。 有关详细信息，请参阅[此网站](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment)。
未经认可的 Linux OS | 服务器可以在 Azure 中启动，但 Azure 不提供任何操作系统支持。 在迁移到 Azure 之前，请考虑升级到[认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
未知操作系统 | 在 vCenter Server 中，将 VM 的操作系统指定为“其他”。 此行为会阻止 Azure Migrate 验证 VM 的 Azure 迁移就绪性。 在迁移服务器之前，请确保 Azur [支持](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements)该操作系统。
不支持的位版本 | 具有 32 位操作系统的 VM 可能会在 Azure 中启动，但建议在迁移到 Azure 之前升级到 64 位。
需要 Microsoft Visual Studio 订阅 | 服务器正在运行仅通过 Visual Studio 订阅支持的 Windows 客户端操作系统。
未找到所需存储性能的 VM | 服务器所需的存储性能（每秒输入/输出操作数 [IOPS] 和吞吐量）超出了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
未找到所需网络性能的 VM | 服务器所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少服务器的网络需求。
未在指定位置找到 VM | 在迁移之前使用不同目标位置。
存在一个或多个不合适的磁盘 | 附加到 VM 的一个或多个磁盘不符合 Azure 要求。<br/><br/> Azure Migrate：发现和评估对磁盘进行评估时，需遵循超级磁盘的磁盘限制 (64 TB)。<br/><br/> 对于附加到 VM 的每个磁盘，请确保磁盘大小 < 64 TB （由超级 SSD 磁盘支持）。<br/><br/> 如果不是这样，请在迁移到 Azure 之前减小磁盘大小，或者在 Azure 中使用多个磁盘并[将它们捆绑到一起](../virtual-machines/premium-storage-performance.md#disk-striping)，以获得更高的存储限制。 请确保 Azure [托管虚拟机磁盘](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)支持每个磁盘所需的性能（IOPS/吞吐量）。
存在一个或多个不合适网络适配器 | 在迁移之前从服务器中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从服务器中删除未使用的磁盘。
磁盘大小超过限制 | Azure Migrate：发现和评估支持高达 64 TB 大小的磁盘（超级磁盘）。 在迁移之前将磁盘缩减为小于 64 TB，或在 Azure 中使用多个磁盘并[将它们捆绑到一起](../virtual-machines/premium-storage-performance.md#disk-striping)，以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误而无法确定磁盘适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 无法找到合适的 VM 类型。 在迁移之前请减少本地服务器的内存和内核数。
由于内部错误而无法确定 VM 适用性 | 请尝试为组创建一个新评估。
由于内部错误而无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于内部错误而无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。
找不到产品/服务币种预留实例 (RI) 的 VM 大小 | 服务器标记为“不适用”，因为找不到所选 RI、产品/服务和货币组合的 VM 大小。 编辑评估属性以选择有效的组合，并重新计算评估。 
Internet 协议有条件就绪 | 仅适用于 Azure VMware 解决方案评估。 Azure VMware 解决方案不支持 IPv6 Internet 地址。 如果检测到你的服务器具有 IPv6，请联系 Azure VMware 解决方案团队以获得修正指导。

## <a name="suggested-migration-tool-in-an-import-based-azure-vmware-solution-assessment-is-unknown"></a>基于导入的 Azure VMware 解决方案评估中建议的迁移工具未知

对于通过 CSV 文件导入的服务器，Azure VMware 解决方案评估中的默认的迁移工具是未知的。 但对于 VMware 环境中的服务器，建议使用 VMWare 混合云扩展 (HCX) 解决方案。 [了解详细信息](../azure-vmware/configure-vmware-hcx.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM 在 Azure VM 评估中“有条件就绪”

对于 VMware 和 Hyper-V VM，Azure VM 评估将 Linux VM 标记为“有条件就绪”，因为存在已知的差距。 

- 该差距使其无法检测本地 VM 上安装的 Linux 操作系统的次版本。
- 例如，对于 RHEL 6.10，当前 Azure VM 评估只检测作为操作系统版本的 RHEL 6。 发生此行为是因为 vCenter Server 和 Hyper-V 主机不提供 Linux VM 操作系统的内核版本。
- 由于 Azure 仅支持特定版本的 Linux，因此 Linux VM 目前在 Azure VM 评估中标记为“有条件就绪”。
- 同时，可以通过查看 [Azure Linux 支持](../virtual-machines/linux/endorsed-distros.md)来确定在本地 VM 上运行的 Linux OS 是否 在 Azure 中经过认可。
- 验证了认可的分发后，可以忽略此警告。

可通过在 VMware VM 上启用[应用程序发现](./how-to-discover-applications.md)来解决这种差距。 Azure VM 评估通过提供的来宾凭据来使用从 VM 中检测到的操作系统。 在 Windows 和 Linux VM 情况下，此操作系统数据标识正确的操作系统信息。

## <a name="operating-system-version-not-available"></a>操作系统版本不可用

对于物理服务器，操作系统次版本信息应该可用。 如果不可用，请联系 Microsoft 支持部门。 对于 VMware 环境中的服务器，Azure Migrate 使用 vCenter Server 中为 VM 指定的操作系统信息。 但 vCenter Server 不提供适用于操作系统的次要版本。 若要发现次要版本，请设置[应用程序发现](./how-to-discover-applications.md)。 对于 Hyper-V VM，不支持操作系统次要版本发现。 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>在 Azure VM 评估中Azure SKU 大于本地

Azure VM 评估可能会建议 Azure VM SKU 具有比当前本地分配更多的内核和内存，具体取决于评估类型：

- VM SKU 建议取决于评估属性。
- 建议受你在 Azure VM 评估中执行的评估类型的影响。 这两种类型是“基于性能”或“根据本地” 。
- 对于基于性能的评估，Azure VM 评估考虑本地 VM 的利用率数据（CPU、内存、磁盘和网络利用率），以确定适合本地 VM 的目标 VM SKU。 在确定有效利用率时，它还添加了舒适因子。
- 对于本地大小调整，不考虑性能数据，并根据本地分配推荐目标 SKU。

让我们看一个示例建议：

现在有具有 4 个内核和 8 GB 内存的本地 VM，CPU 利用率为 50% 且内存利用率为 50%，指定的舒适因子为 1.3。

- 如果评估是“根据本地”，则建议使用具有 4 个内核和 8 GB 内存的 Azure VM SKU。
- 如果评估是基于性能，则根据有效的 CPU 和内存利用率（4 个内核的 50% * 1.3 = 2.6 个内核，8 GB 内存的 50% * 1.3 = 5.3 GB 内存），建议使用具有 4 个内核（最接近的受支持内核数）和 8 GB 内存大小（最接近的受支持的内存大小）的最便宜的 VM SKU。
- [详细了解](concepts-assessment-calculation.md#types-of-assessments)评估调整大小。

## <a name="why-is-the-recommended-azure-disk-sku-bigger-than-on-premises-in-an-azure-vm-assessment"></a>为什么在 Azure VM 评估中建议的 Azure 磁盘 SKU 比本地更大？

Azure VM 评估可能会根据评估类型建议更大的磁盘：

- Azure 的磁盘大小调整取决于两个评估属性：大小调整标准和存储类型。
- 如果大小调整标准是“基于性能”且存储类型设置为“自动”，则当标识目标磁盘类型（标准 HDD、标准 SSD、高级或超级磁盘）时会考虑磁盘的 IOPS 和吞吐量值。  建议使用磁盘类型中的磁盘 SKU，并且该建议考虑到本地磁盘的大小要求。
- 如果大小调整条件是“基于性能”且存储类型为“高级”，则建议根据本地磁盘的 IOPS、吞吐量和大小要求在 Azure 中使用高级磁盘 SKU。  当大小调整标准为“根据本地”且存储类型为“标准 HDD”、“标准 SSD”、“高级”或“超级磁盘”时，使用相同的逻辑进行磁盘大小调整。    

例如，假设本地磁盘的内存为 32 GB，但磁盘的聚合读取和写入 IOPS 为 800 IOPS。 由于 IOPS 要求较高，Azure VM 评估建议使用高级磁盘。 它还建议使用支持所需 IOPS 和大小的磁盘 SKU。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 因此，即使本地磁盘所需的大小为 32 GB，Azure VM 评估也建议使用更大的磁盘，因为本地磁盘的 IOPS 要求很高。

## <a name="why-is-performance-data-missing-for-some-or-all-vms-in-my-assessment-report"></a>为什么我的评估报告中的某些/所有 VM 缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地 VM 的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请确保查看以下内容：

- 是否在创建评估期间启动了 VM。
- 如果只有内存计数器缺失，而你要尝试评估 Hyper-V VM，则请检查这些 VM 上是否启用了动态内存。 由于一个已知问题，当前 Azure Migrate 设备无法收集此类 VM 的内存利用率。
- 如果所有性能计数器都丢失，请确保满足评估的端口访问要求。 详细了解 [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、[Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) 和[物理](./migrate-support-matrix-physical.md#port-access)评估的端口访问要求。
如果丢失任何性能计数器，则 Azure Migrate：发现和评估会回退到本地分配的内核/内存，并相应地建议 VM 大小。

## <a name="why-is-performance-data-missing-for-some-or-all-servers-in-my-azure-vm-or-azure-vmware-solution-assessment-report"></a>为何 Azure VM 或 Azure VMware 解决方案评估报告中的一个或多个服务器缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地服务器的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请确保查看以下内容：

- 是否在创建评估期间启动了服务器。
- 是否只缺少内存计数器，并且你正尝试在 Hyper-V 环境中评估服务器。 在这种情况下，请在服务器上启用动态内存，并重新计算评估以反映最新的更改。 仅当服务器启用了动态内存时，设备才可以在 Hyper-V 环境中收集服务器的内存使用率值。
- 如果所有性能计数器均丢失，确保允许端口 443 (HTTPS) 上的出站连接。

    > [!Note]
    > 如果丢失任何性能计数器，则 Azure Migrate：发现和评估会回退到本地分配的内核/内存，并相应地建议 VM 大小。

## <a name="why-is-performance-data-missing-for-some-or-all-sql-instances-or-databases-in-my-azure-sql-assessment"></a>为什么我的 Azure SQL 评估中的部分/全部 SQL 实例/数据库缺少性能数据？

若要确保收集性能数据，请确保检查：

- 是否在创建评估期间启动了 SQL Server。
- Azure Migrate 中 SQL 代理的连接状态是否为“已连接”，并检查上一个检测信号。 
- “已发现的 SQL 实例”窗格中所有 SQL 实例的 Azure Migrate 连接状态是否均为“已连接”。
- 所有性能计数器是否均丢失；确保允许端口 443 (HTTPS) 上的出站连接。

如果丢失任何性能计数器，Azure SQL 评估会为该实例或数据库推荐最小的 Azure SQL 配置。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>为什么我的评估的置信度分级较低？

根据计算评估所需的[可用数据点](./concepts-assessment-calculation.md#ratings)的百分比，为基于性能的评估计算置信度评级。 评估可能会因为以下原因而导致置信度评级较低：

- 在创建评估的过程中，没有对环境进行分析。 例如，如果创建性能持续时间设置为一周的评估，则在对所有数据点启用发现之后，需要等待至少一周才能收集。 如果无法等待这么久，请将性能持续时间缩短，并重新计算评估。
- 评估无法在评估期内收集部分或所有服务器的性能数据。 若要获得高置信度评级，请确保： 
    - 服务器在评估期间处于开机状态。
    - 允许端口 443 上的出站连接。
    - 为 Hyper-V 服务器启用了动态内存。
    - Azure Migrate 中代理的连接状态为“已连接”。 另请检查最后一个检测信号。
    - 对于 Azure SQL 评估，“已发现的 SQL 实例”窗格中所有 SQL 实例的 Azure Migrate 连接状态均为“已连接”。

    请重新计算评估以反映置信度评级的最新更改。

- 对于 Azure VM 和 Azure VMware 解决方案评估，启动发现后，创建了很少的服务器。 例如，假设对上个月的性能历史记录创建评估，但环境中有一些服务器一周前刚创建。 在这种情况下，整个评估过程中将无法使用新服务器的性能数据，而且置信度评级会较低。 [了解详细信息](./concepts-assessment-calculation.md#confidence-ratings-performance-based)。
- 对于 Azure SQL 评估，很少有 SQL 实例或数据库是在发现开始后创建的。 例如，假设对上个月的性能历史记录创建评估，但环境中有一些 SQL 实例或数据库一周前刚创建。 在这种情况下，整个评估过程中将无法使用新服务器的性能数据，而且置信度评级会较低。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)。

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 评估中是否包含操作系统许可证？

Azure VM 评估目前只考虑 Windows 服务器的操作系统许可成本。 目前未考虑 Linux 服务器的许可成本。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>基于性能的大小调整如何在 Azure VM 评估中工作？

Azure VM 评估不断收集本地服务器的性能数据，并使用它来对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 [了解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based)收集基于性能的数据。

## <a name="can-i-migrate-my-disks-to-an-ultra-disk-by-using-azure-migrate"></a>能否使用 Azure Migrate 将磁盘迁移到超级磁盘？

不是。 目前，Azure Migrate 和 Azure Site Recovery 均不支持迁移到超级磁盘。 在[此网站](https://docs.microsoft.com/azure/virtual-machines/disks-enable-ultra-ssd?tabs=azure-portal#deploy-an-ultra-disk)上查找部署超级磁盘的步骤。

## <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>为何超级磁盘中的预配 IOPS 和吞吐量超过本地 IOPS 和吞吐量？

根据[官方定价页](https://azure.microsoft.com/pricing/details/managed-disks/)，超级磁盘的费用根据预配的大小、IOPS 和吞吐量进行计算。 例如，如果预配了 200 GiB 的超级磁盘（IOPS 为 20,000，1,000 MB/秒），并在 20 小时后将其删除，则它会映射到 256 GiB 的磁盘大小。 将按 256 GiB 大小（IOPS 为 20,000，1,000 MB/秒）收取 20 个小时的费用。

要预配的 IOPS = (发现的吞吐量) *1024/256

## <a name="does-the-ultra-disk-recommendation-consider-latency"></a>超级磁盘建议是否考虑延迟？

否，目前仅磁盘大小、总吞吐量和总 IOPS 用于大小调整和成本计算。

## <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>我可以看到 M 系列支持超级磁盘，但在推荐超级磁盘的评估中，它显示“找不到此位置的 VM”

由于并非所有支持超级磁盘的 VM 大小都存在于所有支持超级磁盘的区域，所以可能会出现此结果。 更改目标评估区域，获取此服务器的 VM 大小。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>为什么我的评估显示一个警告，指出它是通过预留实例、VM 运行时间和折扣 (%) 的无效组合来创建的？

选择“预留实例”时，“折扣(%)”和“VM 运行时间”属性不适用。   当你的评估创建时使用了这些属性的无效组合时，“编辑”按钮和“重新计算”按钮会被禁用。  创建新的评估。 [了解详细信息](./concepts-assessment-calculation.md#whats-an-assessment)。

## <a name="i-dont-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>我看不到物理服务器上的某些网络适配器的性能数据

如果物理服务器启用了 Hyper-V 虚拟化，就会发生此问题。 在这些服务器上，由于产品差距，Azure Migrate 当前同时发现物理和虚拟网络适配器。 仅捕获发现的虚拟网络适配器上的网络吞吐量。

## <a name="the-recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>物理服务器的建议 Azure VM SKU 太大

如果物理服务器启用了 Hyper-V 虚拟化，就会发生此问题。 在这些服务器上，Azure Migrate 当前同时发现物理和虚拟网络适配器。 因此，发现的网络适配器的数量高于实际数量。 由于 Azure VM 评估选择的 Azure VM 可支持所需数量的网络适配器，因此可能会导致 VM 过大。 [了解](./concepts-assessment-calculation.md#calculating-sizing)有关网络适配器数量对大小的影响的详细信息。 未来将解决此产品差距。

## <a name="the-readiness-category-is-marked-not-ready-for-my-physical-server"></a>我的物理服务器的就绪类别为“未就绪”

对于启用了 Hyper-V 虚拟化的物理服务器，就绪类别可能会错误地标记为“未就绪”。 在这些服务器上，由于产品差距，Azure Migrate 当前同时发现物理和虚拟适配器。 因此，发现的网络适配器的数量高于实际数量。 在“根据本地”和“基于性能”的评估中，Azure VM 评估会选取可支持所需数量的网络适配器的 Azure VM。 如果发现的网络适配器数量大于 Azure VM 支持的最大 NIC 数量 32，服务器将被标记为“未就绪”。 [了解](./concepts-assessment-calculation.md#calculating-sizing)有关 NIC 数量对大小的影响的详细信息。

## <a name="the-number-of-discovered-nics-is-higher-than-actual-for-physical-servers"></a>已发现的 NIC 的数量高于物理服务器的实际 NIC 的数量

如果物理服务器启用了 Hyper-V 虚拟化，就会发生此问题。 在这些服务器上，Azure Migrate 当前同时发现物理和虚拟适配器。 因此，已发现的 NIC 数量高于实际数量。

## <a name="capture-network-traffic"></a>捕获网络流量

若要收集网络流量日志，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择 F12 键启动开发人员工具。 如果需要，请清除“清除导航上的条目”设置。
1. 单击“网络”选项卡，然后开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈指示正在捕获流量。 如果未显示红色圆圈，请选择黑色圆圈来启动。
   - 在 Microsoft Edge 和 Internet Explorer 中，记录应自动启动。 如果未启动，请单击绿色播放按钮。
1. 尝试再现该错误。
1. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择“将内容另存为 HAR”。 此操作会将日志压缩并导出为 .har 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中，选择“导出捕获的流量”选项。 此操作会将日志压缩并导出。
1. 选择“控制台”选项卡以查看任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择“另存为”以导出和压缩日志。
   - 在 Microsoft Edge 或 Internet Explorer 中，右键单击错误并选择“全部复制”。
1. 关闭“开发人员工具”。

## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>从何处发现我的评估中的操作系统数据？

- 对于 VMware VM，默认情况下，它是 vCenter Server 提供的操作系统数据。
   - 对于 VMware Linux VM，如果启用了应用程序发现，则从来宾 VM 提取 OS 详细信息。 若要检查评估中有哪些 OS 详细信息，请前往“已发现的服务器”视图，然后将鼠标悬停在“操作系统”列中的值上。  在弹出的文本中，你将能够看到你所看到的 OS 数据是从 vCenter 服务器还是使用 VM 凭据从来宾 VM 中收集的。
   - 对于 Windows VM，将始终从 vCenter 服务器获取操作系统详细信息。
- 对于 Hyper-V VM，操作系统数据是从 Hyper-V 主机上收集的。
- 对于物理服务器，它是从服务器中提取的。

## <a name="common-web-apps-discovery-errors"></a>常见 Web 应用发现错误

Azure Migrate 提供用于评估已发现的 ASP.NET Web 应用的选项，以实现到 Azure 应用服务的迁移，方法是使用“Azure Migrate：发现和评估”工具。 请参阅[评估](tutorial-assess-webapps.md)教程以开始使用。

下表汇总了典型的应用服务评估错误。

| **错误** | **原因** | **建议的操作** |
|--|--|--|
|应用程序池检查|IIS 站点正在使用以下应用程序池：{0}。|对于每个应用服务应用程序，Azure 应用服务不支持多个应用程序池配置。 将工作负载移到单个应用程序池，并删除其他应用程序池。|
|应用程序池标识检查|站点的应用程序池以不受支持的用户标识类型运行：{0}。|应用服务不支持使用 LocalSystem 或 SpecificUser 应用程序池标识类型。 将应用程序池设置为以 ApplicationPoolIdentity 身份运行。|
|授权检查|找到以下不受支持的身份验证类型：{0}。|应用服务支持的身份验证类型和配置不同于本地 IIS。 禁用站点上不支持的身份验证类型。 迁移完成后，可以使用应用服务支持的身份验证类型之一来配置站点。|
|授权检查未知|无法确定所有站点配置的已启用身份验证类型。|无法确定身份验证类型。 修复所有配置错误，并确认管理员组可以访问所有站点内容位置。|
|配置错误检查|发现了以下配置错误：{0}。|在未读取所有适用配置的情况下，无法确定迁移就绪情况。 修复所有配置错误。 请确保配置有效且可访问。|
|内容大小检查|站点内容似乎大于成功迁移所允许的最大值 2 GB。|若要成功迁移，站点内容应小于 2 GB。 对于静态内容，评估站点能否切换到使用基于非文件系统的存储选项，例如 Azure 存储。|
|内容大小检查未知|无法确定文件内容大小，这通常表示访问存在问题。|内容必须可访问才能迁移站点。 确认站点没有对内容使用 UNC 共享，并且管理员组可访问所有站点内容位置。|
|全局模块检查|检测到以下不受支持的全局模块：{0}。|应用服务支持有限的全局模块。 从 GlobalModules 部分删除不支持的模块以及所有相关配置。|
|ISAPI 筛选器检查|检测到以下不支持的 ISAPI 筛选器：{0}。|不支持自定义 ISAPI 筛选器的自动配置。 删除不支持的 ISAPI 筛选器。|
|ISAPI 筛选器检查未知|无法确定所有站点配置的 ISAPI 筛选器是否存在。|不支持自定义 ISAPI 筛选器的自动配置。 修复所有配置错误，并确认管理员组可以访问所有站点内容位置。|
|位置标记检查|在 applicationHost.config 文件中找到以下位置路径：{0}。|迁移方法不支持在 applicationHost.config 中移动位置路径配置。将位置路径配置移到站点的根 web.config 文件，或移动到与应用到的特定应用程序关联的 web.config 文件。|
|协议检查|使用以下不受支持的协议发现了绑定：{0}。|应用服务仅支持 HTTP 和 HTTPS 协议。 删除具有非 HTTP 或 HTTPS 协议的绑定。|
|虚拟目录检查|以下虚拟目录托管在 UNC 共享上：{0}。|迁移不支持迁移 UNC 共享上托管的站点内容。 将内容移动到本地文件路径，或考虑更改到基于非文件系统的存储选项，例如 Azure 存储。 如果使用共享配置，在修改内容路径之前，请禁用服务器的共享配置。|
|HTTPS 绑定检查|应用程序使用 HTTPS。|应用服务中的 HTTPS 配置需要其他手动步骤。 需要执行其他迁移后步骤，才能将证书关联到应用服务站点。|
|**TCP 端口检查**|发现绑定位于以下不受支持的端口：{0}。|应用服务仅支持端口 80 和 443。 向站点发出请求的客户端应将其请求中的端口更新为使用 80 或 443。|
|框架检查|检测到以下非 .NET 框架或不受支持的 .NET 框架版本可能正在由此站点使用：{0}。|迁移不会验证非 .NET 站点的框架。 应用服务支持多个框架，但这些框架具有不同的迁移选项。 确认该站点未使用非 .NET 框架，或考虑使用备用迁移选项。|

## <a name="next-steps"></a>后续步骤

[创建](how-to-create-assessment.md)或[自定义](how-to-modify-assessment.md)评估。
