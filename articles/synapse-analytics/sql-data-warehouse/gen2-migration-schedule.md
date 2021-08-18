---
title: 将专用 SQL 池（以前称为 SQL DW）迁移到 Gen2
description: 将现有专用 SQL 池（以前称为 SQL DW）迁移到 Gen2 以及按区域对迁移进行计划的说明。
services: synapse-analytics
author: rothja
ms.author: jroth
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 59ebd89701f23979a8a359fecfd68a4796bf6d9c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472104"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>将专用 SQL 池（以前称为 SQL DW）升级到 Gen2

Microsoft 有助于降低运行专用 SQL 池（以前称为 SQL DW）的入门级成本。  能够处理高要求查询的较低计算层级现在适用于专用 SQL 池（以前称为 SQL DW）。 请阅读完整的公告：[针对 Gen2 的较低计算层级支持](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)。 新套餐在下表所示区域提供。 对于支持的区域，现有的 Gen1 专用 SQL 池（以前称为 SQL DW）可以通过以下任一方式升级到 Gen2：

- **自动升级过程：** 只要服务在某个地区可用，就不会启动自动升级。  当自动升级在特定区域启动时，将在所选维护计划期间进行单独的数据仓库升级。
- [**自行升级至 Gen2：**](#self-upgrade-to-gen2)可以通过自行升级至 Gen2 来控制何时升级。 如果你的区域尚不受支持，可以从某个还原点直接还原到受支持区域中的 Gen2 实例。

## <a name="automated-schedule-and-region-availability-table"></a>自动计划和区域可用性表

下表按地区汇总了较低的 Gen2 计算层可用的时间以及启动自动升级的时间。 日期随时会变化。 可返回查看，了解所在地区的可用时间。

\* 表示该区域的特定时间表当前不可用。

| **区域** | **较低的 Gen2 可用** | **自动升级开始时间** |
|:--- |:--- |:--- |
| 中国东部 |\* |\* |
| 中国北部 |\* |\* |

## <a name="automatic-upgrade-process"></a>自动升级过程

我们会根据上面的可用性图表，为你的 Gen1 实例安排自动升级。 为了避免专用 SQL 池（以前称为 SQL DW）的可用性发生任何意外中断，将在维护日程安排期间安排自动升级。 在正自动升级到 Gen2 的区域，将禁用新建 Gen1 实例的功能。 自动升级完成后，将弃用 Gen1。 有关计划的详细信息，请参阅[查看维护计划](maintenance-scheduling.md#view-a-maintenance-schedule)

重启专用 SQL 池（以前称为 SQL DW）时，升级过程会导致连接短暂断开（大约 5 分钟）。  重启专用 SQL 池（以前称为 SQL DW）后，它将完全可用。 但是，升级过程继续在后台升级数据文件时，可能会出现性能下降的情况。 性能下降的总时间将根据数据文件的大小而有所不同。

还可以通过在重启后使用更大的 SLO 和资源类在所有主列存储表上运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来加快数据文件升级过程。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

## <a name="self-upgrade-to-gen2"></a>自行升级至 Gen2

可以选择自行升级，方法是在现有 Gen1 专用 SQL 池（以前称为 SQL DW）中执行以下步骤。 如果选择自行升级，则必须在自动升级过程开始之前在所在区域完成它。 这样做可确保避免任何导致冲突的自动升级风险。

进行自行升级时有两种选择。  你可以就地升级当前的专用 SQL 池（以前称为 SQL DW），也可以将 Gen1 专用 SQL 池（以前称为 SQL DW）还原成 Gen2 实例。

- [就地升级](upgrade-to-latest-generation.md) - 此选项会将现有的 Gen1 专用 SQL 池（以前称为 SQL DW）升级到 Gen2。 重启专用 SQL 池（以前称为 SQL DW）时，升级过程会导致连接短暂断开（大约 5 分钟）。  重启后，它将完全可用。 如果在升级过程中遇到问题，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)并引用“Gen2 升级”作为可能原因。
- [从还原点升级](sql-data-warehouse-restore-points.md) - 在当前 Gen1 专用 SQL 池（以前称为 SQL DW）中创建用户定义的还原点，然后直接还原到 Gen2 实例。 现有的 Gen1 专用 SQL 池（以前称为 SQL DW）会保留原样。 还原完成后，Gen2 专用 SQL 池（以前称为 SQL DW）将完全可用。  在已还原的 Gen2 实例上运行所有测试和验证过程后，可以删除原始 Gen1 实例。

  - 步骤 1：在 Azure 门户中，[创建用户定义的还原点](sql-data-warehouse-restore-active-paused-dw.md)。
  - 步骤 2：从用户定义的还原点还原时，将“性能级别”设置为首选的 Gen2 层。

升级过程继续在后台升级数据文件时，可能会经历一段时间的性能下降。 性能下降的总时间将根据数据文件的大小而有所不同。

要加快后台数据迁移进程，可以通过以更大的 SLO 和资源类对要查询的所有主要列存储表运行 [Alter Index rebuild](sql-data-warehouse-tables-index.md) 来立即强制数据移动。

> [!NOTE]
> Alter Index rebuild 是一项脱机操作，在重新生成完成之前，这些表将不可用。

如果遇到与专用 SQL 池（以前称为“SQL DW”）相关的任何问题，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)并引用“Gen2 升级”作为可能原因。

有关详细信息，请参阅[升级到 Gen2](upgrade-to-latest-generation.md)。

## <a name="migration-frequently-asked-questions"></a>迁移常见问题

**问：Gen2 的成本与 Gen1 相同吗？**

- 答：是的。

**问：升级将如何影响我的自动化脚本？**

- 答：引用服务级别目标的任何自动化脚本都应更改为与 Gen2 等效项相对应。  请参阅[此处](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)的详细信息。

**问：自行升级通常需要多长时间？**

- 答：可以就地升级或从还原点升级。

  - 就地升级会导致专用 SQL 池（以前称为 SQL DW）短时间暂停和恢复。  专用 SQL 池（以前称为 SQL DW）联机后，后台进程会继续。  
  - 如果要通过还原点进行升级，则需要更长时间，因为升级将完成整个还原过程。

**问：自动升级需要多长时间？**

- 答：升级的实际停机时间仅为暂停和恢复服务所需的时间，即 5 到 10 分钟。 在短暂的停机时间之后，后台进程将运行存储迁移。 后台进程的时间长度取决于专用 SQL 池（以前称为 SQL DW）的大小。

**问：这种自动升级何时进行？**

- 答：在维护计划期间。 利用选择的维护计划可以最大限度地减少对业务的干扰。

**问：如果我的后台升级过程似乎被卡住了该怎么办？**

- 答：开始为列存储表重新编制索引。 请注意，在此操作期间，为表重新编制索引将处于脱机状态。

**问：如果 Gen2 没有 Gen1 上的服务级别目标怎么办？**

- 答：如果在 Gen1 上运行 DW600 或 DW1200，建议分别使用 DW500c 或 DW1000c，因为 Gen2 提供的内存、资源和性能比 Gen1 更高。

**问：我可以禁用异地备份吗？**

- 答：否。 异地备份是一项企业功能，可在区域变得不可用时保留专用 SQL 池（以前称为 SQL DW）的可用性。 若有其他疑虑，请创建[支持请求](sql-data-warehouse-get-started-create-support-ticket.md)。

**问：Gen1 和 Gen2 之间的 T-SQL 语法有区别吗？**

- 答：从 Gen1 到 Gen2 的 T-SQL 语言语法没有变化。

**问：Gen2 是否支持维护时段？**

- 答：是的。

**问：在我的区域升级后，我能够创建新的 Gen1 实例吗？**

- 答：否。 区域升级后，将禁用新 Gen1 实例的创建。

## <a name="next-steps"></a>后续步骤

- [升级步骤](upgrade-to-latest-generation.md)
- [维护时段](maintenance-scheduling.md)
- [资源运行状况监视器](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [开始迁移前查看](upgrade-to-latest-generation.md#before-you-begin)
- [就地升级和从还原点升级](upgrade-to-latest-generation.md)
- [创建用户定义的还原点](sql-data-warehouse-restore-points.md)
- [了解如何还原到 Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [创建 Azure Synapse Analytics 支持请求](./sql-data-warehouse-get-started-create-support-ticket.md)
