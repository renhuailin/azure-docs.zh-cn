---
title: 自动对数据应用敏感度标签
description: 了解如何创建敏感度标签并在扫描过程中自动将其应用于数据。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 37ac292acc76c681ea38b2ae881ff8cd2ae5ec3c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502440"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>在 Azure Purview 中自动标记数据

本文介绍如何创建 Microsoft 信息保护 (MIP) 敏感度标签，以及如何将其自动应用于 Azure Purview 中的 Azure 资产。

## <a name="what-are-sensitivity-labels"></a>什么是敏感度标签？ 

为了完成工作，组织中的人员会与组织内外的其他人员进行协作。 数据并非始终保存在云中，而是经常在设备、应用和服务之间到处漫游。 

数据漫游时，你希望它以一种安全、受保护的方式进行，以符合组织的业务和合规性策略。

应用敏感度标签可以说明组织中某些数据的敏感度。 例如，特定项目名称在你的组织内可能是高度机密的，而同一术语对其他组织则不是机密。 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure Purview 中的敏感度标签

在 Purview 中，分类类似于主题标签，用于标记和标识扫描期间在数据资产中发现的特定类型的数据。

Purview 使用与 Microsoft 365 相同的分类，也称为敏感信息类型。  MIP 敏感度标签在 Microsoft 365 安全与合规中心 (SCC) 中创建。 这使你可以将现有的敏感度标签扩展到整个 Azure Purview 资产。

分类是直接匹配的，例如社会安全号码，它属于“社会安全号码”分类 。 

相反，如果一个或多个分类和条件同时出现，将应用敏感度标签。 在这种情况下，[条件](/microsoft-365/compliance/apply-sensitivity-label-automatically)指的是可为非结构化数据定义的所有参数，例如与另一分类的接近度以及置信度百分比 。 

使用 Azure Purview 中的敏感度标签，可以将标签自动应用于文件和数据库列。

有关详细信息，请参见:

- Microsoft 365 文档中的[了解敏感度标签](/microsoft-365/compliance/sensitivity-labels)
- [什么是自动标记规则？](#what-are-auto-labeling-rules)
- [Azure Purview 中支持敏感度标签的数据类型](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [标记 SQL 数据库列](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>什么是自动标记规则？

数据在不断增长和变化。 跟踪当前未标记的数据，并采取行动手动应用标签不仅麻烦，而且也是不必要的累赘。 

自动标记规则是你指定的条件，说明应何时应用特定标签。 满足这些条件后，标签就会自动大规模分配给数据，并在数据上保留一致的敏感度标签。

创建标签时，请确保针对[文件](#define-auto-labeling-rules-for-files)和[数据库列](#define-auto-labeling-rules-for-database-columns)定义自动标记规则，以在每次数据扫描时自动应用标签。 

在 Purview 中扫描数据后，可以在“Purview 目录和见解”报表中查看自动应用的标签。
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure Purview 中支持敏感度标签的数据类型

在 Azure Purview 中，以下数据类型支持敏感度标签：

|数据类型  |源  |
|---------|---------|
|自动标记文件     |      - Azure Blob 存储  </br>- Azure Data Lake Storage Gen 1 和 Gen 2  |
|自动标记数据库列     |  - SQL Server </br>- Azure SQL 数据库 </br>- Azure SQL 数据库托管实例   <br> - Azure Synapse  <br> - Azure Cosmos DB <br><br>有关详细信息，请参阅下面的[标记 SQL 数据库列](#labeling-for-sql-database-columns)。  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>标记 SQL 数据库列

除了 Purview 标记数据库列外，Microsoft 还支持使用 [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) 中的 SQL 数据分类标记 SQL 数据库列。 Purview 使用全局 [MIP 敏感度标签](/microsoft-365/compliance/sensitivity-labels)，而 SSMS 仅使用本地定义的标签。

Purview 中的标记和 SSMS 中的标记是独立的进程，当前不会彼此交互。 因此，SSMS 中应用的标签不会显示在 Purview 中，反之亦然。 建议使用 Azure Purview 标记 SQL 数据库，因为它使用可跨多个平台应用的全局 MIP 标签。

有关详细信息，请参阅 [SQL 数据发现和分类文档](/sql/relational-databases/security/sql-data-discovery-and-classification)。

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>如何在 Microsoft 365 中创建敏感度标签

如果还没有敏感度标签，则需要创建它们并使它们可用于 Azure Purview。 此外，也可以修改现有的敏感度标签，以使它们可用于 Azure Purview。

有关详细信息，请参见:

- [许可要求](#licensing-requirements)
- [将敏感度标签扩展到 Azure Purview](#extending-sensitivity-labels-to-azure-purview)
- [创建新的敏感度标签或修改现有标签](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>许可要求

MIP 敏感度标签在 Microsoft 365 安全与合规中心中创建和管理。 若要创建在 Azure Purview 中使用的敏感度标签，必须具有活动的 Microsoft 365 E5 许可证。

如果还没有所需的许可证，则可以注册试用 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion)。

### <a name="extending-sensitivity-labels-to-azure-purview"></a>将敏感度标签扩展到 Azure Purview

默认情况下，MIP 敏感度标签仅适用于 Microsoft 365 中的资产（将其应用于文件和电子邮件）。

若要将 MIP 敏感度标签应用于 Azure Purview 中的 Azure 资产，你必须明确同意扩展标签，并选择希望在 Purview 中可用的特定标签。

通过将 MIP 敏感度标签扩展到 Azure Purview，组织现在可以发现、分类并深入了解更广泛数据源中的敏感度，从而最大程度地降低合规性风险。

> [!NOTE]
> 由于 Microsoft 365 和 Azure Purview 是独立的服务，因此它们可能会部署在不同的区域中。 标签名称和自定义敏感信息类型名称均视为客户数据，默认情况下会保存在同一地理位置，以保护数据的敏感度并避免违反 GDPR 法律。
>
> 因此，默认情况下，不会将标签和自定义敏感信息类型共享给 Azure Purview，并且需要获得你的同意才能在 Azure Purview 中使用它们。

若要将敏感度标签扩展到 Purview，请执行以下步骤：

通过以下步骤，你可以在 Azure Purview 中使用敏感度标签，在 Azure Purview 中可将敏感度标签应用于诸如 SQL 列、Azure Blob 存储中的文件等资产。

1. 在 Microsoft 365 中，导航到“信息保护”页面。 
1. 在“将标签扩展到 Azure Purview 中的资产”中，选择“打开”按钮，然后在显示的确认对话框中选择“是”  。

例如：

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="选择“打开”以将敏感度标签扩展到 Purview" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
将标签扩展到 Azure Purview 中的资产后，可以选择希望在 Purview 中可用的标签。 有关详细信息，请参阅[创建新的敏感度标签或修改现有标签](#creating-new-sensitivity-labels-or-modifying-existing-labels)。
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>创建新的敏感度标签或修改现有标签

当针对 Windows、macOS、iOS 和 Android 上的 Office 应用使用敏感度标签时，用户会在四个小时内看到新标签，而针对 Web 上的 Office，则会在一小时内看到新标签。 但是，允许最多花费 24 小时将更改复制到所有应用和服务。

> [!IMPORTANT]
> 除非你了解某标签对用户的影响，否则不要删除该标签。 有关详细信息，请参阅 Microsoft 365 文档中的[删除标签](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels)。
>

若要创建新的敏感度标签或修改现有标签，请执行以下步骤：

1. 打开 [Microsoft 365 安全与合规中心](https://protection.office.com/homepage)。 

1. 在“解决方案”下，选择“信息保护”，然后选择“创建标签”  。 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="在 Microsoft 365 安全与合规中心内创建敏感度标签" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. 命名标签。 然后，在“定义此标签的范围”下：

    - 在所有情况下，均选择“Azure Purview 资产”。
    - 若要标记文件，请同时选择“文件和电子邮件”。 若仅标记数据库资产，则不需要此选项。 
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="在 Microsoft 365 安全与合规中心内创建标签" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. 按照向导中的其余提示设置标签。 

    具体而言，针对文件和数据库列定义自动标记规则：

    - [定义针对文件的自动标记规则](#define-auto-labeling-rules-for-files)
    - [定义针对数据库列的自动标记规则](#define-auto-labeling-rules-for-database-columns)

    有关向导选项的详细信息，请参阅 Microsoft 365 文档中的[敏感度标签能执行的操作](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do)。

1. 重复上面列出的步骤以创建更多标签。 

    若要创建子标签，请选择父标签 >“...” > “更多操作” > “添加子标签”  。

1. 若要修改现有标签，请浏览至“信息保护” > “标签”，然后选择标签 。 

    然后，选择“编辑标签”以再次打开“编辑敏感度标签”向导，其中包含创建该标签时定义的所有设置 。

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="编辑现有的敏感度标签" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. 所有标签创建完成后，请确保查看标签顺序，并根据需要对其重新排序。 

    若要更改标签的顺序，请选择“...”>“更多操作” > “上移”或“下移”   。 

    有关详细信息，请参阅 Microsoft 365 文档中的[标签优先级（顺序问题）](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters)。


继续[扫描数据以自动应用标签](#scan-your-data-to-apply-labels-automatically)，然后：

- [查看资产上的标签](#view-labels-on-assets)
- [查看“见解”报表中的分类和敏感性标签](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>定义针对文件的自动标记规则

创建或编辑标签时，请在向导中定义针对文件的自动标记规则。 

在“自动标记 Office 应用”页上，启用“自动标记 Office 应用”，然后定义希望自动将标签应用于数据的条件 。

例如：

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="在 Microsoft 365 安全与合规中心内定义针对文件的自动标记规则" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
有关详细信息，请参阅 Microsoft 365 文档中的[自动将敏感度标签应用于数据](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps)。 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>定义针对数据库列的自动标记规则

创建或编辑标签时，请在向导中定义针对数据库列的自动标记规则。 

在“Azure Purview 资产(预览版)”选项下：

1. 选择“自动标记数据库列”滑块。

1. 选择“检查敏感信息类型”以选择要应用于标签的敏感信息类型。

例如：
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="在 Microsoft 365 安全与合规中心内定义针对 SQL 列的自动标记规则" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>扫描数据以自动应用标签

根据定义的自动标记规则，扫描 Azure Purview 中的数据以自动应用创建的标签。 

有关如何设置针对 Azure Purview 中各种资产的扫描的详细信息，请参阅：

|源  |参考  |
|---------|---------|
|**Azure Blob 存储**     |[注册和扫描 Azure Blob 存储](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake 存储**     |[注册和扫描 Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[注册和扫描 Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Azure SQL 数据库**|[注册并扫描 Azure SQL 数据库](register-scan-azure-sql-database.md) </br>[注册和扫描 Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>查看资产上的标签

在 Microsoft 365 中定义标签的自动标记规则并扫描 Azure Purview 中的数据后，标签将自动应用于资产。 

若要在“Azure Purview 目录”中查看应用于资产的标签，请执行以下步骤：

在“Azure Purview 目录”中，使用“标签”筛选选项以仅显示带有特定标签的文件。 例如： 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="按标签搜索资产" lightbox="media/create-sensitivity-label/filter-search-results.png":::

例如：

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="查看 Azure Blob 存储中文件上的敏感度标签" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>查看“见解”报表中的分类和敏感性标签

使用“分类”和“敏感度标签”报表，在 Azure Purview 中查找有关已分类和已标记数据的见解 。

> [!div class="nextstepaction"]
> [分类见解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度标签见解](sensitivity-insights.md)
