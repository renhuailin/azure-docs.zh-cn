---
title: 集成 Azure 数据资源管理器以实现长期日志保留 | Microsoft Docs
description: 将 Azure Sentinel 日志发送到 Azure 数据资源管理器进行长期保留，以降低数据存储成本。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 1f9d0a146c557f50c0cea776c6e3ae6853377df5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748073"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>集成 Azure 数据资源管理器以实现长期日志保留

默认情况下，引入 Azure Sentinel 的日志存储在 Azure Monitor Log Analytics 中。 本文介绍如何通过将日志发送到 Azure 数据资源管理器进行长期保留来降低 Azure Sentinel 中的保留成本。

将日志存储在 Azure 数据资源管理器中可降低成本，同时仍可查询数据，这在数据增长时特别有用。 例如，尽管安全数据可能会随着时间的推移而丢失价值，但你可能需要保留日志以满足法规要求或对旧数据运行定期调查。

## <a name="about-azure-data-explorer"></a>关于 Azure 数据资源管理器

Azure 数据资源管理器是一个大数据分析平台，针对日志和数据分析进行了高度优化。 由于 Azure 数据资源管理器使用 Kusto 查询语言 (KQL) 作为其查询语言，因此这是 Azure Sentinel 数据存储的一个不错的选择。 通过将 Azure 数据资源管理器用于数据存储，你可以运行跨平台查询，并跨 Azure 数据资源管理器和 Azure Sentinel 可视化数据。

有关详细信息，请参阅 Azure 数据资源管理器[文档](/azure/data-explorer/)和[博客](https://azure.microsoft.com/blog/tag/azure-data-explorer/)。

### <a name="when-to-integrate-with-azure-data-explorer"></a>何时与 Azure 数据资源管理器集成

Azure Sentinel 提供完整的 SIEM 和 SOAR 功能、快速部署和配置，以及用于 SOC 团队的高级内置安全功能。 但是，当 SOC 用户访问更新数据的频率高于 Azure Sentinel 中存储安全数据的值时，此值可能会在几个月后丢弃。

如果你只需偶尔访问特定表（例如出于定期调查或审核目的），则可能考虑到将数据保留在 Azure Sentinel 中不再是经济高效的方案。 此时，我们建议将数据存储在 Azure 数据资源管理器中，这样做成本较低，但仍可使用在 Azure Sentinel 中运行的相同 KQL 查询进行浏览。

使用 [Log Analytics Azure 数据资源管理器代理功能](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)，可以直接从 Azure Sentinel 访问 Azure 数据资源管理器中的数据。 为此，请在日志搜索或工作簿中使用跨群集查询。

> [!IMPORTANT]
> 核心 SIEM 功能（包括分析规则、UEBA 和调查图）不支持 Azure 数据资源管理器中存储的数据。
>

> [!NOTE]
> 与 Azure 数据资源管理器集成还能让你在数据中获得控制和粒度。 有关详细信息，请参阅[设计注意事项](#design-considerations)。
>
## <a name="send-data-directly-to-azure-sentinel-and-azure-data-explorer-in-parallel"></a>直接将数据并行发送到 Azure Sentinel 和 Azure 数据资源管理器

建议在 Azure Sentinel 中保留任何具有安全值的数据，以用于检测、事件调查、威胁搜寻、UEBA 等。 在 Azure Sentinel 中保留此数据主要可使安全运营中心 (SOC) 用户受益，通常 3-12 个月的存储便已足够。

还可以将所有数据（不考虑其安全值）配置为同时发送到 Azure 数据资源管理器，在那里可将其存储更长时间。 尽管将数据同时发送到 Azure Sentinel 和 Azure 数据资源管理器会导致某些重复，但降低 Azure Sentinel 中的保留成本可以显著降低成本。

> [!TIP]
> 利用此选项，还可以关联分布在不同数据存储中的数据，以便用 Azure 数据资源管理器中存储的操作性数据或长期数据来丰富 Azure Sentinel 中存储的安全数据，或实现其他目的。 有关详细信息，请参阅[使用 Azure Monitor 跨资源查询 Azure 数据资源管理器](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)。
>

下图显示了如何将所有数据保留在 Azure 数据资源管理器中，同时仅将安全数据发送到 Azure Sentinel 进行日常使用。

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="将数据并行存储在 Azure 数据资源管理器和 Azure Sentinel 中。":::

有关实现此体系结构选项的详细信息，请参阅 [Azure 数据资源管理器监视](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)。

## <a name="export-data-from-log-analytics-into-azure-data-explorer"></a>将数据从 Log Analytics 导出到 Azure 数据资源管理器

可以选择通过 Azure 事件中心或 Azure 数据工厂将数据从 Log Analytics 导出到 Azure 数据资源管理器，而不是直接将数据发送到 Azure 数据资源管理器。

### <a name="data-export-architecture"></a>数据导出体系结构

下图显示了通过 Azure Monitor 引入管道导出的数据的示例流。 默认情况下，你的数据将定向到 Log Analytics，但也可以将其配置为导出到 Azure 存储帐户或事件中心。

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="从 Azure Monitor 导出数据 - 体系结构。":::

配置数据导出规则时，请选择要导出的日志类型。 配置后，到达 Log Analytics 引入终结点并以所选表的工作区为目标的新数据将被导出到存储帐户或事件中心。

配置要导出的数据时，请注意以下事项：

|注意事项  | 详细信息 |
|---------|---------|
|**导出数据的范围**     |  为特定表配置了导出后，将导出发送到该表的所有数据，但不会出现异常。 不支持导出已筛选的部分数据或限制导出到特定事件。       |
|**位置要求**     |   Azure Monitor/Azure Sentinel 工作区和目标位置（Azure 存储帐户或事件中心）必须位于同一个地理区域。      |
|**受支持的表**     | 并非所有表都支持导出，例如自定义日志表就不支持。 <br><br>有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出](../azure-monitor/logs/logs-data-export.md)和[受支持表的列表](../azure-monitor/logs/logs-data-export.md#supported-tables)。         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>数据导出方法和过程

使用以下过程之一将数据从 Azure Sentinel 导出到 Azure 数据资源管理器中：

- **通过 Azure 事件中心**。 将数据从 Log Analytics 导出到事件中心，可以在那里将数据引入 Azure 数据资源管理器。 此方法可将一些数据（前 X 个月）存储在 Azure Sentinel 和 Azure 数据资源管理器中。

- **通过 Azure 存储和 Azure 数据工厂**。 将数据从 Log Analytics 导出到 Azure Blob 存储，然后使用 Azure 数据工厂运行定期复制作业，进一步将数据导出到 Azure 数据资源管理器。 通过此方法，可以仅在 Azure Sentinel/Log Analytics 中的数据接近其保留限制时才从 Azure 数据工厂复制数据，从而避免重复。

### <a name="azure-event-hub"></a>[Azure 事件中心](#tab/adx-event-hub)

本部分介绍如何将 Azure Sentinel 数据从 Log Analytics 导出到事件中心，在那里可将数据引入 Azure 数据资源管理器。 与[直接将数据并行发送到 Azure Sentinel 和 Azure 数据资源管理器](#send-data-directly-to-azure-sentinel-and-azure-data-explorer-in-parallel)类似，此方法包含一些数据重复，因为数据会在到达 Log Analytics 时被流式传输到 Azure 数据资源管理器。

下图显示了将数据导出到事件中心的示例流，数据将从事件中心引入 Azure 数据资源管理器。

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="通过 Azure 事件中心将数据导出到 Azure 数据资源管理器。":::

上图中显示的体系结构为必须经常（每 X 个月）访问的数据提供了完整的 Azure Sentinel SIEM 体验，包括事件管理、视觉调查、威胁搜寻、高级可视化效果、UEBA 等。 同时，得益于 Azure 数据资源管理器代理功能，此体系结构还使你能够通过直接在 Azure 数据资源管理器中访问长期数据或通过 Azure Sentinel 来对其进行查询。 Azure 数据资源管理器中的长期数据存储的查询无需任何更改即可从 Azure Sentinel 移植到 Azure 数据资源管理器。

> [!NOTE]
> 当通过事件中心将多个数据表导出到 Azure 数据资源管理器时，请记住，Log Analytics 数据导出对于每个命名空间的最大事件中心数有限制。 有关数据导出的详细信息，请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出](../azure-monitor/logs/logs-data-export.md?tabs=portal)。
>
> 对于大多数客户，我们建议使用事件中心标准层。 根据需要导出的表数量以及这些表的流量大小，可能需要使用事件中心专用层。 有关详细信息，请参阅[事件中心文档](../event-hubs/event-hubs-quotas.md)。
>

> [!TIP]
> 有关此过程的详细信息，请参阅[教程：在 Azure 数据资源管理器中引入和查询监视数据](/azure/data-explorer/ingest-data-no-code)。
>

**通过事件中心将数据导出到 Azure 数据资源管理器**：

1. **配置到事件中心的 Log Analytics 数据导出**。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出](../azure-monitor/logs/logs-data-export.md)。

1. **创建 Azure 数据资源管理器群集和数据库**。 有关详细信息，请参阅：

    - [创建 Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal)
    - [为 Azure 数据资源管理器群集选择正确的计算 SKU](/azure/data-explorer/manage-cluster-choose-sku)

1. **创建目标表**。 原始数据首先引入到中间表，其中存储、操作和展开原始数据。

    更新策略（与应用于所有新数据的函数类似）用于将已展开的数据引入到最终表中，该表与 Azure Sentinel 中的原始表具有相同的架构。

    将原始表的保留期设置为 0 天。 数据仅存储在格式正确的表中，并在转换后立即从原始表中删除。

    有关详细信息，请参阅[在 Azure 数据资源管理器中引入和查询监视数据](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)。

1. <a name="mapping"></a>**创建表映射**。 映射 JSON 表，以定义记录从事件中心传入时如何进入原始事件表。 有关详细信息，请参阅[为指标和日志数据创建更新策略](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)。

1. **创建更新策略并将其附加到原始记录表**。 在此步骤中，创建一个名为“更新策略”的函数，并将其附加到目标表，以便在引入时转换数据。

    > [!NOTE]
    > 仅当你希望在 Azure 数据资源管理器中具有与在 Azure Sentinel 中相同架构和格式的数据表时，才需要执行此步骤。
    >

    有关详细信息，请参阅[将事件中心连接到 Azure 数据资源管理器](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)。

1. **在事件中心和 Azure 数据资源管理器中的原始数据表之间创建数据连接**。 配置 Azure 数据资源管理器，并提供如何将数据导出到事件中心的详细信息。

    使用 [Azure 数据资源管理器文档](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)中的说明，并指定以下详细信息：

    - **目标**。 指定包含原始数据的特定表。
    - **格式**。 将 `.json` 指定为表格式。
    - **要应用的映射**。 指定在上面的[步骤 4](#mapping) 中创建的映射表。


1. **修改目标表的保留期**。 [默认 Azure 数据资源管理器保留策略](/azure/data-explorer/kusto/management/retentionpolicy)中的时间可能远远超过你所需的时间。

    使用以下命令将保留策略更新为一年：

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure 存储/Azure 数据工厂](#tab/azure-storage-azure-data-factory)

本部分介绍如何将 Azure Sentinel 数据从 Log Analytics 导出到 Azure 存储，Azure 数据工厂可在其中运行常规作业以将数据导出到 Azure 数据资源管理器。

通过使用 Azure 存储和 Azure 数据工厂，可以仅在 Azure Sentinel/Log Analytics 中的数据接近其保留限制时才从 Azure 存储复制数据。 这样做没有重复数据，并且 Azure 数据资源管理器仅用于访问超出 Azure Sentinel 保留限制的数据。

> [!TIP]
> 虽然将 Azure 存储和 Azure 数据工厂用于陈旧数据的体系结构更复杂，但此方法可提供更大的整体成本节约。
>
下图显示了将数据导出到 Azure 存储的示例流，Azure 数据工厂在其中运行常规作业，进一步将数据导出到 Azure 数据资源管理器中。

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="通过 Azure 存储和 Azure 数据工厂将数据导出到 Azure 数据资源管理器。":::

**通过 Azure 存储和 Azure 数据工厂将数据导出到 Azure 数据资源管理器**：

1. **配置到事件中心的 Log Analytics 数据导出**。 有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出](../azure-monitor/logs/logs-data-export.md?tabs=portal#enable-data-export)。

1. **创建 Azure 数据资源管理器群集和数据库**。 有关详细信息，请参阅：

    - [创建 Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal)
    - [为 Azure 数据资源管理器群集选择正确的计算 SKU](/azure/data-explorer/manage-cluster-choose-sku)

1. **创建目标表**。 原始数据首先引入到中间表，其中存储、操作和展开原始数据。

    更新策略（与应用于所有新数据的函数类似）用于将已展开的数据引入到最终表中，该表与 Azure Sentinel 中的原始表具有相同的架构。

    将原始表的保留期设置为 0 天。 数据仅存储在格式正确的表中，并在转换后立即从原始表中删除。

    有关详细信息，请参阅[在 Azure 数据资源管理器中引入和查询监视数据](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)。

1. <a name="mapping"></a>**创建表映射**。 映射 JSON 表，以定义记录从事件中心传入时如何进入原始事件表。 有关详细信息，请参阅[为指标和日志数据创建更新策略](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)。

1. **创建更新策略并将其附加到原始记录表**。 在此步骤中，创建一个名为“更新策略”的函数，并将其附加到目标表，以便在引入时转换数据。

    > [!NOTE]
    > 仅当你希望在 Azure 数据资源管理器中具有与在 Azure Sentinel 中相同架构和格式的数据表时，才需要执行此步骤。
    >

    有关详细信息，请参阅[将事件中心连接到 Azure 数据资源管理器](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)。

1. **在事件中心和 Azure 数据资源管理器中的原始数据表之间创建数据连接**。 配置 Azure 数据资源管理器，并提供如何将数据导出到事件中心的详细信息。

    使用 [Azure 数据资源管理器文档](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)中的说明，并指定以下详细信息：

    - **目标**。 指定包含原始数据的特定表。
    - **格式**。 将 `.json` 指定为表格式。
    - **要应用的映射**。 指定在上面的[步骤 4](#mapping) 中创建的映射表。

1. **设置 Azure 数据工厂管道**：

    - 为 Azure 存储和 Azure 数据资源管理器创建链接服务。 有关详细信息，请参阅：

        - [使用 Azure 数据工厂在 Azure Blob 存储中复制和转换数据](../data-factory/connector-azure-blob-storage.md)
        - [使用 Azure 数据工厂向/从 Azure 数据资源管理器复制数据](../data-factory/connector-azure-data-explorer.md)。

    - 从 Azure 存储创建数据集。 有关详细信息，请参阅 [Azure 数据工厂中的数据集](../data-factory/concepts-datasets-linked-services.md)。

    - 基于 LastModifiedDate 属性，使用复制操作创建数据管道。

        有关详细信息，请参阅[使用 Azure 数据工厂根据 LastModifiedDate 复制新的和已更改的文件](../data-factory/solution-template-copy-new-files-lastmodifieddate.md)。

---

## <a name="design-considerations"></a>设计注意事项

在 Azure 数据资源管理器中存储 Azure Sentinel 数据时，请考虑以下元素：

|注意事项  |说明  |
|---------|---------|
|**群集大小和 SKU**     | 仔细规划群集中的节点数和 VM SKU。 这些因素将决定处理能力和热缓存（SSD 和内存）的大小。 缓存越大，便能够以更高性能查询更多数据。 <br><br>我们建议你访问 [Azure 数据资源管理器大小调整计算器](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)，你可以在其中进行不同的配置，并查看产生的成本。 <br><br>Azure 数据资源管理器还具有自动缩放功能，可基于群集负载来根据需要明智地添加/删除节点。 有关详细信息，请参阅[在 Azure 数据资源管理器中管理群集水平缩放（横向扩展）群集以适应不断变化的需求](/azure/data-explorer/manage-cluster-horizontal-scaling)。      |
|**热/冷缓存**     | Azure 数据资源管理器提供对热缓存中数据表的控制，还可以更快地返回结果。 如果 Azure 数据资源管理器群集中有大量数据，则建议按月份对表进行细分，以便更精细地显示热缓存中的数据。 <br><br>有关详细信息，请参阅[缓存策略（热缓存和冷缓存）](/azure/data-explorer/kusto/management/cachepolicy)       |
|**保留**     |   在 Azure 数据资源管理器中，可以配置从数据库或单个表中删除数据的时间，这也是限制存储成本的重要部分。 <br><br> 有关详细信息，请参阅[保留策略](/azure/data-explorer/kusto/management/retentionpolicy)。       |
|**安全性**     |  几项 Azure 数据资源管理器设置可以帮助你保护数据，例如标识管理、加密等。 Azure 数据资源管理器专门用于基于角色的访问控制 (RBAC)，可以将其配置为将访问权限限制到数据库、表甚至是表中的行。 有关详细信息，请参阅 [Azure 数据资源管理器中的安全性](/azure/data-explorer/security)和[行级别安全性](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)。|
|**数据共享**     |   通过 Azure 数据资源管理器，你可以向其他方（如合作伙伴或供应商）提供数据片段，甚至可以从其他方购买数据。 有关详细信息，请参阅[使用 Azure Data Share 与 Azure 数据资源管理器共享数据](/azure/data-explorer/data-share)。      |
| **其他成本组成部分** | 对于以下方法，请考虑其他成本组成部分： <br><br>通过 Azure 事件中心导出数据： <br>- Log Analytics 数据导出成本，按导出的 GB 数收费。 <br>- 事件中心成本，按吞吐量单位收费。  <br><br>通过 Azure 存储和 Azure 数据工厂导出数据： <br>- Log Analytics 数据导出，按导出的 GB 数收费。 <br>- Azure 存储，按存储的 GB 数收费。 <br>- Azure 数据工厂，按活动运行的副本收费。
|     |         |

## <a name="next-steps"></a>后续步骤

无论你在何处存储数据，都可以使用 Azure Sentinel 继续搜寻和调查。

有关详细信息，请参阅：

- [教程：使用 Azure Sentinel 调查事件](investigate-cases.md)
- [使用 Azure Sentinel 搜寻威胁](hunting.md)