---
title: 来自 Azure Synapse Analytics 的元数据和世系
description: 本文介绍如何连接 Azure Synapse Analytics 和 Azure Purview 来跟踪数据世系。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 02ea61356157c7e2a421c736a1af5bd87a1bcb5c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212322"
---
# <a name="how-to-get-lineage-from-azure-synapse-analytics-into-azure-purview"></a>如何从 Azure Synapse Analytics 获取世系再转至 Azure Purview

本文档介绍将 Azure Synapse 工作区与 Azure Purview 帐户连接以跟踪数据世系所需的步骤。 本文档还详细介绍了覆盖范围和受支持的世系功能。

## <a name="supported-azure-synapse-capabilities"></a>受支持的 Azure Synapse 功能

目前，Azure Purview 从以下 Azure Synapse 管道活动捕获运行时世系：

- [复制数据](../data-factory/copy-activity-overview.md?context=/azure/synapse-analytics/context/context)
- [数据流](../data-factory/concepts-data-flow-overview.md?context=/azure/synapse-analytics/context/context)

> [!IMPORTANT]
> 如果源或目标使用不受支持的数据存储系统，则 Azure Purview 将删除世系。

[!INCLUDE[azure-synapse-supported-activity-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

## <a name="access-secured-azure-purview-account"></a>访问受保护的 Azure Purview 帐户
      
如果你的 Purview 帐户受防火墙保护，请了解如何使 Azure Synapse 能够通过 Purview 专用终结点[访问受保护的 Purview 帐户](../synapse-analytics/catalog-and-governance/how-to-access-secured-purview-account.md)。

## <a name="bring-azure-synapse-lineage-into-purview"></a>将 Azure Synapse 世系引入 Purview

### <a name="step-1-connect-azure-synapse-workspace-to-your-purview-account"></a>步骤 1：将 Azure Synapse 工作区连接到 Purview 帐户

可以将 Azure Synapse 工作区连接到 Purview，该连接使 Azure Synapse 能够将世系信息推送到 Purview。 按照[将 Synapse 工作区连接到 Azure Purview](../synapse-analytics/catalog-and-governance/quickstart-connect-azure-purview.md) 中的步骤进行操作。 多个 Azure Synapse 工作区可以连接到同一个 Azure Purview 帐户，以进行整体世系跟踪。

### <a name="step-2-run-pipeline-in-azure-synapse-workspace"></a>步骤 2：在 Azure Synapse 工作区中运行管道

可以在 Azure Synapse 工作区中使用复制活动创建管道。 对于世系数据捕获，无需任何其他配置。 在活动执行期间，将自动捕获世系数据。

### <a name="step-3-monitor-lineage-reporting-status"></a>步骤 3：监视世系报告状态

运行 Azure Synapse 管道后，在 Synapse 管道监视视图中，可以通过选择以下“世系状态”按钮来检查世系报告状态。 活动输出 JSON -> `reportLineageToPurvew` 部分中也提供了相同的信息。

:::image type="content" source="../data-factory/media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="在管道监视视图中监视世系报告状态。":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>步骤 4：在 Purview 帐户中查看世系信息

在 Purview 帐户中，可以浏览资产并选择类型“Azure Synapse Analytics”。 还可以使用关键字搜索数据目录。

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-assets.png" alt-text="Browse the Azure Synapse assets in Purview."

选择“Synapse 账户”->“管道”->“活动”，可以查看世系信息。

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png" alt-text="在 Purview 中浏览 Azure Synapse 管道世系。" lightbox="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png":::

## <a name="next-steps"></a>后续步骤

[目录世系用户指南](catalog-lineage-user-guide.md)

[链接到 Azure Data Share 以获取世系](how-to-link-azure-data-share.md)
