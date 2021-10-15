---
title: 操作指南：搜索数据目录
description: 本文概述了如何搜索 Azure Purview 数据目录。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0473c75678631fee158cfabd33406b863971bf97
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455707"
---
# <a name="search-the-azure-purview-data-catalog"></a>搜索 Azure Purview 数据目录

对数据进行了扫描并将其引入到 Azure Purview 数据映射后，数据使用者需要轻松找到其分析或管理工作负载所需的数据。 数据发现可能很耗时，因为你可能不知道在哪里查找你需要的数据。 甚至在找到数据后，你也可能不确定是否能信任该数据以及是否能与其建立依赖关系。

Azure Purview 中的搜索目标是快速找到重要数据，从而加快数据发现的过程。 本文概述了如何搜索 Azure Purview 数据目录以快速找到正在查找的数据。

## <a name="search-the-catalog-for-assets"></a>在目录中搜索资产

可以从 Purview Studio UX 的顶部栏快速访问搜索栏。 在数据目录主页中，搜索栏位于屏幕中心。

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="屏幕截图显示 Azure Purview 搜索栏位置" border="true":::

单击搜索栏后，此时将显示搜索历史记录以及数据目录中最近访问的资产。 这样就可以从之前已经完成的数据浏览中快速选取。

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="屏幕截图显示在输入任何关键字之前的搜索栏" border="true":::

输入可帮助标识资产的关键字，如名称、数据类型、分类和术语表术语。 输入搜索关键字时，Purview 会动态建议可能满足你需求的资产和搜索。 若要完成搜索，请单击“查看搜索结果”或按“Enter”。

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="屏幕截图显示用户输入关键字时的搜索栏" border="true":::

输入搜索关键字后，Purview 会返回一个用户作为数据读取者的数据资产列表，与输入的关键字相匹配。

Purview 相关性引擎会整理所有匹配项，基于它认为对用户有用的程度对匹配项进行排序。 例如，与未批注的文件夹相比，数据使用者可能对匹配多个关键字的表（数据专员分配了术语表术语并提供了描述）更感兴趣。 许多因素决定了资产的相关性分数，Purview 搜索团队正在不断优化相关性引擎，以确保排名靠前的搜索结果具有价值。

如果排名靠前的结果不包括你要查找的资产，可以使用左侧的分面按业务元数据（例如术语表术语、分类和包含集合）进行筛选。 如果你对特定数据源类型（例如 Azure Data Lake Storage Gen2 或 Azure SQL 数据库）感兴趣，可以使用源类型椭圆筛选器缩小搜索范围。

> [!NOTE]
> 搜索将仅返回你作为数据读取者或管护者的集合中的资产。 有关详细信息，请参阅[创建和管理集合](how-to-create-and-manage-collections.md)。

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="显示搜索结果的屏幕截图" border="true":::

对于某些注释，可以单击省略号以在 AND 条件或 OR 条件之间选择。 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="显示如何在 AND 或 OR 条件之间进行选择的屏幕截图" border="true":::

找到要查找的资产后，可以选择它来查看详细信息，例如架构、世系和详细的分类列表。 若要详细了解资产详细信息页面中的信息，请参阅[管理目录资产](catalog-asset-details.md)。

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="显示资产详细信息页的屏幕截图" border="true":::

## <a name="bulk-edit-search-results"></a>批量编辑搜索结果

如果希望对搜索返回的多个资产进行更改，可以利用 Azure Purview 批量修改术语表术语、分类和联系人。 若要了解详细信息，请参阅[批量编辑资产](how-to-bulk-edit-assets.md)指南。

## <a name="browse-the-data-catalog"></a>浏览数据目录

如果知道要查找的内容，搜索就非常适合，但有时数据使用者希望浏览可供他们使用的数据。 Azure Purview 数据目录提供了浏览体验，使用户能够按集合或遍历目录中每个数据源的层次结构来浏览可用的数据。 有关详细信息，请参阅[浏览数据目录](how-to-browse-catalog.md)。

## <a name="search-query-syntax"></a>搜索查询语法

所有搜索查询都由关键字和运算符组成。 关键字是资产属性的一部分。 可能的关键字可以是分类、术语表术语、资产说明或资产名称。 关键字可以只是要匹配的属性的一部分。 使用下面列出的关键字和运算符来确保 Azure Purview 返回正在查找的资产。 

下面是可用于构成搜索查询的运算符。 可以在单个查询中根据需要多次组合运算符。

| 操作员 | 定义 | 示例 |
| -------- | ---------- | ------- |
| OR | 指定资产必须至少具有这两个关键字中的一个关键字。 必须全部大写。 空格也是 OR 运算符。  | 查询 `hive OR database` 将返回包含“hive”和/或“database”的资产。 |
| AND | 指定资产必须同时具有两个关键字。 必须全部大写 | 查询 `hive AND database` 将返回同时包含“hive”和“database”的资产。 |
| NOT | 指定资产不能包含 NOT 子句右边的关键字 | 查询 `hive NOT database` 将返回包含“hive”但不包含“database”的资产。 |
| () | 将一组关键字和运算符组合在一起。 组合使用多个运算符时，可使用括号来指定运算顺序。 | 查询 `hive AND (database OR warehouse)` 将返回包含“hive”以及“database”和/或“warehouse”的资产。 |
| "" | 指定短语中查询必须匹配的确切内容。 | 查询 `"hive database"` 将返回其属性中包含“hive database”短语的资产 |
| * | 匹配一个或多个字符的通配符。 不能是关键字的第一个字符。 | 查询 `dat*` 将返回含有以“dat”开头（例如“data”或“database”）的属性的资产。 |
| ? | 匹配单个字符的通配符。 不能是关键字的第一个字符 | 查询 `dat?` 将返回含有以“dat”开头并且由四个字母组成（例如“date”或“data”）的属性的资产。 |

> [!Note]
> 始终全部以大写字母指定布尔运算符（AND、OR、NOT）  。 否则，大小写无关紧要，额外的空格也无关紧要。

## <a name="next-steps"></a>后续步骤

- [如何创建、导入和导出术语表术语](how-to-create-import-export-glossary.md)
- [如何管理业务术语表的术语模板](how-to-manage-term-templates.md)
