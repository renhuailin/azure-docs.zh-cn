---
title: 如何自动将敏感度标签应用于 Azure Purview 中的数据
description: 了解如何创建敏感度标签并在扫描过程中自动将其应用于数据。
author: ajaykar
ms.author: ajaykar
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 98c1dba49283cd1f7a9e1c4c748af37b9a4fac02
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219263"
---
# <a name="how-to-automatically-apply-sensitivity-labels-to-your-data-in-azure-purview"></a>如何自动将敏感度标签应用于 Azure Purview 中的数据

## <a name="create-or-extend-existing-sensitivity-labels-to-azure-purview"></a>为 Azure Purview 创建敏感度标签或扩展其现有敏感度标签

> [!IMPORTANT]
> Azure Purview 敏感度标签目前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

如果还没有敏感度标签，则需要创建它们并使它们可用于 Azure Purview。 此外，也可以修改现有的敏感度标签，以使它们可用于 Azure Purview。

### <a name="step-1-licensing-requirements"></a>步骤 1：许可要求

在 Microsoft 365 合规中心创建和管理敏感度标签。 若要创建在 Azure Purview 中使用的敏感度标签，必须具有可提供自动应用敏感度标签权益的有效 Microsoft 365 许可证。

有关许可证的完整列表，请参阅 [Azure Purview 中的敏感度标签常见问题解答](sensitivity-labels-frequently-asked-questions.yml)。 如果还没有所需的许可证，则可以注册试用 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion)。

### <a name="step-2-consent-to-use-sensitivity-labels-in-azure-purview"></a>步骤 2：同意在 Azure Purview 中使用敏感度标签

以下步骤将扩展敏感度标签并使其在 Azure Purview 中可供使用。可以在 Azure Purview 中将敏感度标签应用于文件和数据库列。

1. 在 Microsoft 365 中，导航到“信息保护”页面。</br>
   如果你最近预配了信息保护的订阅，可能需要在几个小时后才会显示“信息保护”页。
1. 在“将标签扩展到 Azure Purview 中的资产”区域中，选择“打开”按钮，然后在显示的确认对话框中选择“是”  。

例如：

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-small.png" alt-text="选择“打开”按钮以将敏感度标签扩展到 Purview" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview.png":::

将标签扩展到 Azure Purview 中的资产后，可以在 Purview 中使用所有已发布的敏感度标签。

### <a name="step-3-create-or-modify-existing-label-to-automatically-label-content"></a>步骤 3：创建标签或修改现有标签以自动标记内容

若要创建新的敏感度标签或修改现有标签，请执行以下步骤：

1. 打开 [Microsoft 365 安全与合规中心](https://compliance.microsoft.com/)。

1. 在“解决方案”下，选择“信息保护”，然后选择“创建标签”  。

    :::image type="content" source="media/how-to-automatically-label-your-content/create-sensitivity-label-full-small.png" alt-text="在 Microsoft 365 合规中心创建敏感度标签" lightbox="media/how-to-automatically-label-your-content/create-sensitivity-label-full.png":::

1. 命名标签。 然后，在“定义此标签的范围”下：

    - 在所有情况下，均选择“Azure Purview 资产”。
    - 若要标记文件，请同时选择“文件和电子邮件”。 若仅标记数据库资产，则不需要此选项

    :::image type="content" source="media/how-to-automatically-label-your-content/create-label-scope-small.png" alt-text="在 Microsoft 365 合规中心自动进行标记" lightbox="media/how-to-automatically-label-your-content/create-label-scope.png":::

1. 按照其余的提示配置标签设置。

    具体而言，针对文件和数据库列定义自动标记规则：

    - [定义针对文件的自动标记规则](#autolabeling-for-files)
    - [定义针对数据库列的自动标记规则](#autolabeling-for-database-columns)

    有关配置选项的详细信息，请参阅 Microsoft 365 文档中的[敏感度标签的作用](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do)。

1. 重复上面列出的步骤以创建更多标签。

    若要创建子标签，请选择父标签 >“...” > “更多操作” > “添加子标签”  。

1. 若要修改现有标签，请浏览至“信息保护” > “标签”，然后选择标签 。

    然后选择“编辑标签”以再次打开“编辑敏感度标签”配置，其中包含创建该标签时定义的所有设置 。

    :::image type="content" source="media/how-to-automatically-label-your-content/edit-sensitivity-label-full-small.png" alt-text="编辑现有的敏感度标签" lightbox="media/how-to-automatically-label-your-content/edit-sensitivity-label-full.png":::

1. 所有标签创建完成后，请确保查看标签顺序，并根据需要对其重新排序。

    若要更改标签的顺序，请选择“...”>“更多操作” > “上移”或“下移”   。

    有关详细信息，请参阅 Microsoft 365 文档中的[标签优先级（顺序问题）](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters)。

#### <a name="autolabeling-for-files"></a>针对文件的自动标记

创建或编辑标签时，请定义针对文件的自动标记规则。

在“自动标记 Office 应用”页上，启用“自动标记 Office 应用”，然后定义希望自动将标签应用于数据的条件 。

例如：

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files-small.png" alt-text="在 Microsoft 365 合规中心定义针对文件的自动标记规则" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files.png":::

有关详细信息，请参阅 Microsoft 365 文档中的[自动将敏感度标签应用于数据](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps)。

#### <a name="autolabeling-for-database-columns"></a>针对数据库列的自动标记

创建或编辑标签时，请定义针对数据库列的自动标记规则。

在“数据库列”选项下：

1. 选择“自动标记数据库列”滑块。

1. 选择“检查敏感信息类型”以选择要应用于标签的敏感信息类型。

例如：

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns-small.png" alt-text="在 Microsoft 365 合规中心定义针对 SQL 列的自动标记规则" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns.png":::

### <a name="step-4-publish-labels"></a>步骤 4：发布标签

创建标签后，需要根据定义的自动标记规则，扫描 Azure Purview 中的数据以自动应用创建的标签。

## <a name="scan-your-data-to-apply-sensitivity-labels-automatically"></a>扫描数据以自动应用敏感度标签

根据定义的自动标记规则，扫描 Azure Purview 中的数据以自动应用创建的标签。 等待最长 24 小时，让敏感度标签更改反映在 Purview 中。

有关如何设置针对 Azure Purview 中各种资产的扫描的详细信息，请参阅：

|源  |参考  |
|---------|---------|
|存储中的文件 | [注册和扫描 Azure Blob 存储](register-scan-azure-blob-storage-source.md) </br> [注册和扫描 Azure 文件存储](register-scan-azure-files-storage-source.md)[注册和扫描 Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[注册和扫描 Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)</br>[注册和扫描 Amazon S3](register-scan-amazon-s3.md) |
|**数据库列** | [注册并扫描 Azure SQL 数据库](register-scan-azure-sql-database.md) </br>[注册和扫描 Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md) </br> [注册和扫描专用 SQL 池](register-scan-azure-synapse-analytics.md)</br> [注册和扫描 Azure Synapse Analytics 工作区](register-scan-azure-synapse-analytics.md) </br> [注册并扫描 Azure Cosmos 数据库 (SQL API)](register-scan-azure-cosmos-database.md) </br> [注册和扫描 Azure MySQL 数据库](register-scan-azure-mysql-database.md) </br> [注册和扫描 Azure Database for PostgreSQL](register-scan-azure-postgresql.md) |
| | |

## <a name="view-labels-on-assets-in-the-catalog"></a>查看目录中资产的标签

在 Microsoft 365 中定义标签的自动标记规则并扫描 Azure Purview 中的数据后，标签将自动应用于资产。

若要在“Azure Purview 目录”中查看应用于资产的标签，请执行以下步骤：

在“Azure Purview 目录”中，使用“标签”筛选选项以仅显示带有特定标签的资产。 例如：

:::image type="content" source="media/how-to-automatically-label-your-content/filter-search-results-small.png" alt-text="按标签搜索资产" lightbox="media/how-to-automatically-label-your-content/filter-search-results.png":::

若要查看某个资产的详细信息（包括找到的分类和应用的标签），请在结果中单击该资产。

例如：

:::image type="content" source="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage-small.png" alt-text="查看 Azure Blob 存储中文件上的敏感度标签" lightbox="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>查看“见解”报表中的分类和敏感性标签

使用“分类”和“敏感度标签”报告，在 Azure Purview 中查找有关已分类和已标记数据的见解 。

> [!div class="nextstepaction"]
> [分类见解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度标签见解](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Azure Purview 中的标记概述](create-sensitivity-label.md)

> [!div class="nextstepaction"]
> [标记常见问题解答](sensitivity-labels-frequently-asked-questions.yml)