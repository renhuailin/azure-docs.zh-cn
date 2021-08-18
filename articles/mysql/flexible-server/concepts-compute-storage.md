---
title: 计算和存储选项 - Azure Database for MySQL（灵活服务器）
description: 本文介绍 Azure Database for MySQL（灵活服务器）中的计算和存储选项。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 9b8699598a9bac4781346ff939736b2bd6ee72f2
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113429937"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器（预览版）中的计算和存储选项

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

可以在以下三个不同的计算层之一中创建 Azure Database for MySQL 灵活服务器：“可突增”、“常规用途”和“内存优化”。 计算层通过基础 VM SKU 使用的 B 系列、D 系列和 E 系列进行区分。 计算层和大小的选择决定服务器上可用的内存和 Vcore。 所有计算层使用相同的存储技术。 所有资源都在 MySQL 服务器级别预配。 一个服务器可以有一个或多个数据库。

| 资源/层 | **可突发** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| VM 系列| B 系列 | Ddsv4 系列 | Edsv4 系列|
| vCore 数 | 1, 2 | 2、4、8、16、32、48、64 | 2、4、8、16、32、48、64 |
| 每个 vCore 的内存 | 变量 | 4 GiB | 8 GiB * |
| 存储大小 | 20 GiB 到 16 TiB | 20 GiB 到 16 TiB | 20 GiB 到 16 TiB |
| 数据库备份保留期 | 1 到 35 天 | 1 到 35 天 | 1 到 35 天 |

\* E64ds_v4（内存优化）SKU 除外，它具有 504 GB 内存

可以从下表着手来选择计算层。

| 计算层 | 目标工作负荷 |
|:-------------|:-----------------|
| 可突发 | 最适合不需要连续使用完整 CPU 的工作负载。 |
| 常规用途 | 大多数业务工作负荷。此类工作负荷需要均衡的计算和内存以及可缩放的 I/O 吞吐量。 相关示例包括用于托管 Web 和移动应用的服务器，以及其他企业应用程序。|
| 内存优化 | 高性能数据库工作负荷。此类工作负荷需要内存中性能来实现更快的事务处理速度和更高的并发性。 相关示例包括用于处理实时数据的服务器，以及高性能事务性应用或分析应用。|

创建服务器后，计算层、计算大小和存储大小会发生更改。 计算缩放需要重启，并且需要 60-120 秒，而存储缩放不需要重启。 还可以独立调高或调低备份保持期。 有关详细信息，请参阅[缩放资源](#scale-resources)部分。

## <a name="compute-tiers-size-and-server-types"></a>计算层、大小和服务器类型

可以根据层和大小选择计算资源。 这可确定 Vcore 和内存大小。 vCore 表示底层硬件的逻辑 CPU。

可用服务器类型的详细规格如下：

| 计算大小         | vCore 数 | 内存大小 (GiB) | 支持的最大 IOPS | 支持的最大 I/O 带宽 (MBps)| 最大连接数
|----------------------|--------|-------------------| ------------------ |-----------------------------------|------------------
| **可突发**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 171
| Standard_B1ms        | 1      | 2                 | 640                | 10                                | 341
| Standard_B2s         | 2      | 4                 | 1280               | 15                                | 683
| **常规用途**  |        |                   |                    |                                   | 
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                | 1365
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                | 2731
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               | 5461
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               | 10923
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               | 21845
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              | 32768
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              | 43691
| **内存优化** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                | 2731
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                | 5461
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               | 10923
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               | 21845
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               | 43691
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              | 65536
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              | 86016

若要获取有关可用计算系列的更多详细信息，请参阅[可突发（B 系列）](../../virtual-machines/sizes-b-series-burstable.md)、[常规用途（Ddsv4 系列）](../../virtual-machines/ddv4-ddsv4-series.md)和[内存优化（Edsv4 系列）](../../virtual-machines/edv4-edsv4-series.md)的相关 Azure VM 文档。

>[!NOTE]
>对于[可突发（B 系列）计算层](../../virtual-machines/sizes-b-series-burstable.md)，如果 VM 启动/停止或重启，额度可能会丢失。 有关详细信息，请参阅[可突发（B 系列）常见问题解答](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart)。

## <a name="storage"></a>存储

预配的存储是指可供灵活服务器使用的存储容量。 存储用于数据库文件、临时文件、事务日志和 MySQL 服务器日志。 在所有计算层中，支持的最小存储为 20 GiB，最大存储为 16 TiB。 存储按 1 GiB 的增量缩放，并且可在创建服务器后纵向扩展。

>[!NOTE]
> 存储只能增加，不能减少。

可以使用存储限制、存储百分比和存储使用的指标在 Azure 门户中（使用 Azure Monitor）监视存储使用情况。 请参阅[监视文章](./concepts-monitoring.md)了解相关指标。 

### <a name="reaching-the-storage-limit"></a>达到存储限制

当服务器上使用的存储接近预配的上限时，服务器将进入只读模式，以保护服务器上任何丢失的写入。 如果可用存储小于预配存储大小的 5%，则预配存储小于等于 100 GiB 的服务器将标记为只读。 对于预配存储超出 100 GiB 的服务器，当可用存储不到 5 GiB 时，会将该服务器标记为只读。

例如，如果已预配 110 GiB 的存储，而实际使用量超过 105 GiB，则会将服务器标记为只读。 或者，如果已预配 5 GiB 的存储，则当可用存储少于 256 MB 时，服务器会标记为只读。

当服务试图将服务器标记为只读时，会阻止所有新的写入事务请求，现有的活动事务将继续执行。 当服务器设置为只读时，所有后续写入操作和事务提交均会失败。 读取查询将继续不间断工作。 

若要使服务器退出只读模式，应增加服务器上预配的存储。 可以使用 Azure 门户或 Azure CLI 来完成此操作。 增加后，服务器将准备好再次接受写入事务。

我们建议你设置警报，以便在服务器存储接近阈值时通知你，从而可以避免进入只读状态。 请参阅[监视文章](./concepts-monitoring.md)了解可用指标。 

我们建议你 <!--turn on storage auto-grow or to--> 设置警报，以便在服务器存储接近阈值时通知你，从而可以避免进入只读状态。 有关详细信息，请参阅有关[如何设置警报](how-to-alert-on-metric.md)的警报文档。

### <a name="storage-auto-grow"></a>存储自动增长

存储自动增长可防止服务器耗尽存储空间并变为只读。 如果启用了存储自动增长，存储会在不影响工作负荷的情况下自动增长。 默认情况下，会为所有新创建的服务器启用存储自动增长。 对于预配存储小于等于 100 GB 的服务器，当可用存储小于预配存储的 10% 时，预配存储大小会增加 5 GB。 对于预配存储大于 100 GB 的服务器，可用存储空间小于预配存储大小 10 GB 时，预配存储大小会增加 5%。 适用上面指定的最大存储限制。

例如，如果已预配 1000 GB 的存储，而实际使用量超过 990 GB，则服务器存储大小会增加到 1050 GB。 或者，如果已预配 10 GB 的存储，则当可用存储少于 1 GB 时，存储大小会增加到 15 GB。

请记住，存储只能纵向扩展，不能纵向缩减

## <a name="iops"></a>IOPS

Azure Database for MySQL - 灵活服务器支持预配额外的 IOPS。 借助此功能可配置超出免费 IOPS 限制的其他 IOPS。 使用此功能，你可以根据工作负载需求随时增加或减少预配的 IOPS 数。 

所有计算大小的最小 IOPS 为 360，最大 IOPS 取决于所选计算大小。 在预览版中，支持的最大 IOPS 为 20,000 IOPS。

详细了解每个计算大小的最大 IOPS，如下所示： 

| 计算大小         | 最大 IOPS        | 
|----------------------|---------------------|
| **可突发**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **常规用途**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **内存优化** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

最大 IOPS 取决于每个计算大小的最大可用 IOPS。 参阅 [B 系列](../../virtual-machines/sizes-b-series-burstable.md)、[Ddsv4 系列](../../virtual-machines/ddv4-ddsv4-series.md)和 [Edsv4 系列](../../virtual-machines/edv4-edsv4-series.md)文档中的“最大未缓存磁盘吞吐量：IOPS/MBps”列。

> [!Important]
> 免费 IOPS = MINIMUM（计算大小的“最大未缓存磁盘吞吐量：IOPS/MBps”，300 + 预配的存储 (GiB) * 3）<br>
> 所有计算大小的最小 IOPS 均为 360<br>
> 最大 IOPS 取决于所选的计算大小。 在预览版中，支持的最大 IOPS 为 20,000 IOPS。

可以使用 [IO 百分比](./concepts-monitoring.md)指标在 Azure 门户中（使用 Azure Monitor）监视 I/O 使用情况。 如果需要比基于计算力的最大 IOPS 更高的 IOPS，则需要扩展服务器的计算。

## <a name="backup"></a>Backup

服务自动对服务器进行备份。 可以选择 1 到 35 天的保持期。 从[备份和还原概念文章](concepts-backup-restore.md)详细了解备份。

## <a name="scale-resources"></a>缩放资源

创建服务器之后，可以独立地更改计算层、计算大小（vCore 数和内存）、存储量和备份保持期。 计算大小可以纵向扩展或缩减。 备份保持期可以在 1 到 35 天范围内增减。 存储大小只能增加。 可以通过门户或 Azure CLI 缩放资源。

> [!NOTE]
> 存储大小只能增加。 增加后，将不能返回到更小的存储大小。

更改计算层或计算大小时，将重启服务器，使新的服务器类型生效。 在系统切换到新服务器的短暂期间，无法建立新的连接，所有未提交的连接将会回退。 此时段不定，但大多数情况下为 60-120 秒。 

缩放存储和更改备份保持期是联机操作，无需重启服务器。

## <a name="pricing"></a>定价

有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/MySQL/)。 若要查看所需配置的具体成本，可以单击 [Azure 门户](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers)的“计算 + 存储”选项卡，系统就会根据选定的选项显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择“添加项”，展开“数据库”类别，选择“Azure Database for MySQL”和“灵活服务器”作为部署类型以自定义选项   。

如果想要优化服务器成本，可以考虑以下提示：

- 如果计算未充分利用，则缩减计算层或计算大小 (vCore)。
- 如果工作负载不需要连续使用“常规用途”和“内存优化”层中的全部计算容量，请考虑切换到“可突发”计算层。
- 在未使用时停止服务器。
- 如果不需要较长的备份保持期，请缩短备份保持期。

## <a name="next-steps"></a>后续步骤

- 了解如何[在门户中创建 MySQL 服务器](quickstart-create-server-portal.md)。
- 了解[服务限制](concepts-limitations.md)。
