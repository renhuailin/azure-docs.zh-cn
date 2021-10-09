---
title: Azure Purview 中的标记
description: 开始利用敏感度标签和分类来增强 Purview 资产
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 7a94d82c3ec2a47869c64520e20bed6cdf58ebdb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210511"
---
# <a name="labeling-in-azure-purview"></a>Azure Purview 中的标记

> [!IMPORTANT]
> Azure Purview 敏感度标签目前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

为了完成工作，组织中的人员会与组织内外的其他人员进行协作。 数据并非始终保存在云中，而是经常在设备、应用和服务之间到处漫游。 当你的数据处于漫游状态时，你仍希望按照组织的业务与合规策略为其提供安全保护。</br>

将敏感度标签应用于你的内容，可以通过指明组织中特定数据的敏感程度来确保数据安全。 这样还会抽象化数据本身，使你可以使用标签来跟踪数据的类型，而无需在另一个平台上透露敏感数据。</br>

例如，将敏感度标签“高度机密”应用于包含社会安全号码和信用卡号的文档有助于识别文档的敏感度，而不需要知道文档中的实际数据。

## <a name="benefits-of-labeling-in-azure-purview"></a>Azure Purview 中的标记的优点

Azure Purview 允许你将敏感度标签应用于资产，使你能够对数据进行分类和保护。

* 标签随数据一起传输：目前可以在 Microsoft 365、SharePoint、Teams、Power BI 和 SQL 中识别 Purview 中使用的敏感度标签。 如果在 Purview 中的资产上应用了某个标签，将数据传输到任何其他平台（例如 Power BI 或 Office）时，该标签也会随数据一起传输。 同样，如果在 Word 文档上应用某个标签，然后 Purview 扫描该文档，则该标签会流向 Purview。
* 数据资产概述：Purview 通过预先准备的报告提供数据见解。 当你扫描 Purview 中的数据时，我们将使用有关你拥有的资产、扫描历史记录、在数据中找到的分类、应用的标签、术语表术语等信息来生成报告。
* 自动标记：可以基于数据敏感度自动应用标签。 扫描资产中的敏感数据时，将使用自动标记规则来确定要应用哪个敏感度标签。 可为每个敏感度标签创建自动标记规则，定义构成标签的分类/敏感信息类型。
* 将标签应用于文件和数据库列：可将标签应用于 Azure Data Lake、Azure 文件存储等存储中的文件以及架构化数据（例如 Azure SQL DB、Cosmos DB 等中的列）。

敏感度标签是可应用于资产以便对数据进行分类和保护的标签。 在[此处](/microsoft-365/compliance/create-sensitivity-labels.md)详细了解敏感度标签。

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>如何将标签应用于 Azure Purview 中的资产

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="将标签应用于 Azure Purview 中的资产的流程。创建标签、注册资产、扫描资产、查找分类、应用标签。":::

若要将标签应用于 Azure Purview 中的资产，需要执行以下步骤：

1. 在 Microsoft 365 合规中心，[为 Azure Purview 创建敏感度标签或扩展其现有敏感度标签](how-to-automatically-label-your-content.md)。 创建敏感度标签的过程包括创建自动标记规则，以便告知我们应该根据在数据中找到的分类应用哪个标签。
1. 在 Azure Purview 中[注册和扫描资产](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically)。
1. Azure Purview 应用分类：当你针对某个资产计划扫描时，Azure Purview 会扫描该资产中的数据类型，并在数据目录中对这些数据应用分类。 应用分类的过程将由 Azure Purview 自动完成，你无需执行任何操作。
1. Azure Purview 应用标签：在资产上找到分类后，Azure Purview 将根据自动标记规则向资产应用标签。 应用标签的过程将由 Azure Purview 自动完成，只要你在步骤 1 中创建了具有自动标记规则的标签，就不需要执行任何操作。

> [!NOTE]
> 自动标记规则是你指定的条件，指明何时应用特定的标签。 如果满足这些条件，则将标签自动分配到数据。 创建标签时，请确保针对文件和数据库列定义自动标记规则，以在每次扫描时自动应用标签。
>

## <a name="supported-data-sources"></a>支持的数据源

在 Azure Purview 中，以下数据源支持敏感度标签：

|数据类型  |源  |
|---------|---------|
|自动标记文件     |    - Azure Blob 存储</br>- Azure 文件存储</br>- Azure Data Lake Storage Gen 1 和 Gen 2</br>- Amazon S3|
|自动标记数据库列     |  - SQL Server</br>- Azure SQL 数据库</br>- Azure SQL 数据库托管实例</br>- Azure Synapse Analytics 工作区</br>- Azure Cosmos DB (SQL API)</br> - Azure Database for MySQL</br> - Azure Database for PostgreSQL</br> - Azure 数据资源管理器</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>SQL 数据库标记

除了 Purview 标记数据库列外，Microsoft 还支持使用 [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) 中的 SQL 数据分类标记 SQL 数据库列。 Purview 使用全局 [MIP 敏感度标签](/microsoft-365/compliance/sensitivity-labels)，而 SSMS 仅使用本地定义的标签。

Purview 中的标记和 SSMS 中的标记是独立的进程，当前不会彼此交互。 因此，SSMS 中应用的标签不会显示在 Purview 中，反之亦然。 建议使用 Azure Purview 标记 SQL 数据库，因为它使用可跨多个平台应用的全局 MIP 标签。

有关详细信息，请参阅 [SQL 数据发现和分类文档](/sql/relational-databases/security/sql-data-discovery-and-classification)。 </br></br>

> [!div class="nextstepaction"]
> [如何自动标记内容](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [敏感度标签见解](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [标记常见问题解答](sensitivity-labels-frequently-asked-questions.yml)
