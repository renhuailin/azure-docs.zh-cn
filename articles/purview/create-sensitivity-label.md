---
title: 自动将敏感标签应用于你的数据
description: 了解如何创建敏感度标签，并在扫描过程中自动将它们应用到数据。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: b286533da90565429f96c5701aaa72dab0fb21d0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552294"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>在 Azure 监控范围中自动标记数据

本文介绍如何创建 Microsoft 信息保护 (MIP) 敏感性标签，并将其自动应用于 Azure 监控范围中的 Azure 资产。

## <a name="what-are-sensitivity-labels"></a>什么是敏感度标签？ 

若要完成工作，你的组织中的人员将与组织内部和外部的其他人协作。 数据并非始终保留在你的云中，而是经常漫游到各种设备、应用程序和服务。 

数据漫游后，你希望它以一种安全的、受保护的方式满足你的组织的业务和合规性策略。

通过应用敏感度标签，可以陈述组织中特定数据的敏感程度。 例如，特定的项目名称在你的组织中可能高度保密，而此同一术语对于其他组织不是机密的。 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure 监控范围中的敏感度标签

在监控范围中，分类类似于主题标记，用于标记和标识在扫描过程中发现的特定类型的数据。

监控范围使用与 Microsoft 365 相同的分类（也称为敏感信息类型）。  在 Microsoft 365 安全和合规性中心 (SCC) 中创建 MIP 敏感度标签。 这使你能够在 Azure 监控范围资产之间扩展现有敏感度标签。

> [!NOTE]
> 尽管分类直接匹配 (社会保障号码) **社会安全号码** 的分类，但当找到一个或多个分类和方案时，将应用敏感标签。 
> 

可以使用 Azure 监控范围中的敏感度标签来执行以下操作：

- **自动将标签应用** 于文件和数据库列

- **控制** 谁可以和不能访问你的数据

- 在标记的数据上 **强制实施保护设置**，例如加密

有关详细信息，请参阅：

- 了解 Microsoft 365 文档中的[敏感度标签](/microsoft-365/compliance/sensitivity-labels)
- [什么是 autolabeling 规则？](#what-are-autolabeling-rules)
- [Azure 监控范围中的敏感标签支持的数据类型](#supported-data-types-for-sensitivity-labels-in-azure-purview)

#### <a name="what-are-autolabeling-rules"></a>什么是 autolabeling 规则？

你的数据不断增长和变化。 跟踪当前未标记的数据，并采取措施手动应用标签并不麻烦，但也是一种不必要的麻烦。 

Autolabeling 规则是你指定的条件，用于说明何时应应用特定标签。 满足这些条件时，会自动将标签分配给数据，并按比例保留数据中一致的敏感度标签。

创建标签时，请确保为 " [文件](#define-autolabeling-rules-for-files) " 和 " [数据库" 列](#define-autolabeling-rules-for-database-columns) 都定义 autolabeling 规则，以便在每次数据扫描时自动应用标签。 

在监控范围中扫描数据后，可以在 "监控范围目录" 和 "见解报表" 中查看自动应用的标签。

#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure 监控范围中的敏感标签支持的数据类型

以下数据类型的 Azure 监控范围支持敏感度标签：

|数据类型  |源  |
|---------|---------|
|自动为文件添加标签     |      - Azure Blob 存储  </br>-Azure Data Lake Storage 第1代和第2代  |
|自动为数据库列添加标签     |  -SQL server </br>-Azure SQL 数据库 </br>-Azure SQL 数据库托管实例   <br> -Azure Synapse  <br> - Azure Cosmos DB   |
| | |

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>如何在 Microsoft 365 中创建敏感度标签

如果还没有敏感度标签，则需要创建它们，并将其提供给 Azure 监控范围。 还可以修改现有的敏感度标签，使其可用于 Azure 监控范围。

有关详细信息，请参阅：

- [许可要求](#licensing-requirements)
- [将敏感标签扩展到 Azure 监控范围](#extending-sensitivity-labels-to-azure-purview)
- [创建新的敏感度标签或修改现有标签](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>许可要求

在 Microsoft 365 安全性和符合性中心创建和管理 MIP 敏感度标签。 若要创建在 Azure 监控范围中使用的敏感度标签，您必须拥有活动 Microsoft 365 E5 许可证。

如果还没有必需的许可证，则可以注册 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion)的试用版。

### <a name="extending-sensitivity-labels-to-azure-purview"></a>将敏感标签扩展到 Azure 监控范围

默认情况下，MIP 敏感度标签仅适用于 Microsoft 365 中的资产，你可以将其应用于文件和电子邮件。

若要将 MIP 敏感度标签应用于 Azure 监控范围中的 Azure 资产，必须明确同意扩展标签，并选择要在监控范围中使用的特定标签。

通过使用 Azure 监控范围扩展 MIP 的敏感度标签，组织现在可以发现、分类并深入了解各种数据源，从而最大程度地降低合规性风险。

> [!NOTE]
> 由于 Microsoft 365 和 Azure 监控范围是不同的服务，因此可能会将它们部署在不同的区域中。 标签名称和自定义敏感信息类型名称被视为客户数据，并在默认情况下保留在同一地理位置，以保护数据的敏感性并避免 GDPR 的法律。
>
> 出于此原因，默认情况下，将不会在 Azure 监控范围中共享标签和自定义敏感信息类型，并要求你同意在 Azure 监控范围中使用它们。

> [!IMPORTANT]
> 你同意允许 Microsoft 将标签名称和自定义敏感信息类型名称 *共享到 Azure 监控范围和 Azure 安全* 中心 (ASC) 。 Microsoft 使用 Azure 监控范围中的标签信息来丰富 ASC 中的建议和警报。 
>
> 同意 Microsoft 365 符合性中心适用于与这两个服务共享此数据。 目前不能选择仅与 Azure 监控范围共享标签信息。

**将敏感度标签扩展到监控范围：**

在 Microsoft 365 中，导航到 " **信息保护** " 页。 在 "将 **标签扩展到 Azure 中的资产监控范围**" 中，选择 " **打开** " 按钮，然后在显示的确认对话框中选择 **"是"** 。

例如：

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="选择 * * 开启 * * 以将敏感度标签扩展到监控范围" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
将标签扩展到 Azure 监控范围中的资产后，可以选择要在监控范围中使用的标签。 有关详细信息，请参阅 [创建新的敏感度标签或修改现有标签](#creating-new-sensitivity-labels-or-modifying-existing-labels)。
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>创建新的敏感度标签或修改现有标签

1. 打开 [Microsoft 365 安全和合规中心](https://protection.office.com/homepage)。 

1. 在 " **解决方案**" 下，选择 " **信息保护**"，然后选择 " **创建标签**"。 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="在 Microsoft 365 安全性和符合性中心创建敏感度标签" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. 命名标签。 然后，在 " **定义此标签的作用域**" 下，选择 " **文件和电子邮件** 和 **Azure 监控范围资产**"。
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="在 Microsoft 365 安全性和符合性中心创建标签" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. 按照向导中的其余提示设置标签设置。 

    具体而言，为文件和数据库列定义 autolabeling 规则：

    - [定义文件的 autolabeling 规则](#define-autolabeling-rules-for-files)
    - [定义数据库列的 autolabeling 规则](#define-autolabeling-rules-for-database-columns)

    有关向导选项的详细信息，请参阅 Microsoft 365 文档中的 [敏感度标签](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) 。

1. 重复上述步骤以创建其他标签。 

    若要创建子标签，请 **选择 >**  >  的父标签 **更多操作**  > **添加子标签**。

1. 若要修改现有标签，请浏览到 **信息保护**  >  **标签**，并选择标签。 

    然后选择 " **编辑标签** " 以再次打开 " **编辑敏感度标签** " 向导，并在创建标签时定义所有设置。

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="编辑现有敏感度标签" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. 创建完所有标签后，请确保查看标签顺序，并根据需要对其重新排序。 

    若要更改标签的顺序，**请选择** **> 更多操作**  >  **上移** 或下移。 **Move down.** 

    有关详细信息，请参阅 Microsoft 365 文档中的 [标签优先级 (订单重要) ](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) 。

> [!IMPORTANT]
> 除非您了解对用户的影响，否则请不要删除标签。 
>
> 有关详细信息，请参阅 [删除和删除](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) Microsoft 365 文档中的标签。

继续 [扫描数据以自动应用标签](#scan-your-data-to-apply-labels-automatically)，然后执行以下操作：

- [查看资产上的标签](#view-labels-on-assets)
- [查看分类和敏感度标签的见解报告](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>定义文件的 autolabeling 规则

创建或编辑标签时，为向导中的文件定义 autolabeling 规则。 

在 " **office 应用程序的自动标记** " 页上， **为 Office 应用启用自动标记，** 然后定义你希望标签自动应用于数据的条件。

例如：

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="在 Microsoft 365 安全性和符合性中心定义文件的 autolabeling 规则" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
有关详细信息，请参阅 Microsoft 365 文档中的 [自动应用敏感标签到数据](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) 。 

#### <a name="define-autolabeling-rules-for-database-columns"></a>定义数据库列的 autolabeling 规则

创建或编辑标签时，在向导中为数据库列定义 autolabeling 规则。 

在 **Azure 监控范围资产 (预览版)** 选项：

1. 为 " **数据库列** " 滑块选择 "自动标记"。

1. 选择 " **检查敏感信息类型** "，选择要应用于标签的敏感信息类型。

例如：
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="在 Microsoft 365 安全性和符合性中心定义 SQL 列的 autolabeling 规则" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>扫描数据以自动应用标签

扫描 Azure 监控范围中的数据，根据定义的 autolabeling 规则自动应用已创建的标签。 

有关如何在 Azure 监控范围中的各种资产上设置扫描的详细信息，请参阅：

|源  |参考  |
|---------|---------|
|**Azure Blob 存储**     |[注册并扫描 Azure Blob 存储](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake 存储**     |[注册和扫描 Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[注册和扫描 Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Azure SQL 数据库**|[注册并扫描 Azure SQL 数据库](register-scan-azure-sql-database.md) </br>[注册并扫描 Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md)|
|**防火墙之后的存储帐户**     |[在 Azure 监控范围中扫描防火墙之后的存储帐户](scan-sqlresource-firewall.md)         |
| | |

## <a name="view-labels-on-assets"></a>查看资产上的标签

为 Microsoft 365 中的标签定义了 autolabeling 规则并将数据扫描到 Azure 监控范围后，会自动将标签应用到资产。 

**查看 Azure 监控范围目录中应用于资产的标签：**

在 Azure 监控范围目录中，使用 **标签** 筛选选项显示仅具有特定标签的文件。 例如： 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="按标签搜索资产" lightbox="media/create-sensitivity-label/filter-search-results.png":::

例如：

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="查看 Azure Blob 存储中文件的敏感度标签" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>查看分类和敏感度标签的见解报告

使用 **分类** 和 **敏感度标记** 报表，在 Azure 监控范围中查找有关已分类和已标记数据的见解。

> [!div class="nextstepaction"]
> [分类见解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度标签见解](sensitivity-insights.md)


