---
title: 操作指南：搜索数据目录
description: 本文概述如何搜索数据目录。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564907"
---
# <a name="search-the-azure-purview-data-catalog"></a>搜索 Azure Purview 数据目录

对数据使用者来说，数据发现是数据分析或数据管理工作负载的第一步。 数据发现可能很耗时，因为你可能不知道在哪里查找你需要的数据。 甚至在找到数据后，你也可能不确定是否能信任该数据以及是否能与其建立依赖关系。

Azure Purview 中的搜索目标是快速找到重要数据，从而加快数据发现的过程。 本文概述了如何搜索 Azure Purview 数据目录以快速找到正在查找的数据。

## <a name="search-the-catalog-for-assets"></a>在目录中搜索资产

在 Azure Purview 中，搜索栏位于 Purview 工作室 UX 的顶部。

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="屏幕截图显示 Azure Purview 搜索栏位置" border="true":::

单击搜索栏时，可以看到近期搜索历史记录和最近访问的资产。 选择“查看全部”以查看最近查看的所有资产。

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="屏幕截图显示在输入任何关键字之前的搜索栏" border="true":::

输入可帮助标识资产的关键字，如名称、数据类型、分类和术语表术语。 输入与所需资产相关的关键字时，Azure Purview 会显示有关搜索内容和可能的资产匹配项的建议。 若要完成搜索，请单击“查看搜索结果”或按“Enter”。

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="屏幕截图显示用户输入关键字时的搜索栏" border="true":::

搜索结果页将按相关性顺序显示与提供的关键字匹配的资产列表。 有多种因素可能会影响资产的相关性分数。 可以选择特定数据存储、分类、联系人、标签以及适用于正在查找的资产的术语表术语，进一步筛选列表。

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="显示搜索结果的屏幕截图" border="true":::

 单击所需资产以查看该资产的详细信息页，可在其中查看包括架构、世系和资产所有者在内的属性。

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="显示资产详细信息页的屏幕截图" border="true":::

## <a name="search-query-syntax"></a>搜索查询语法

所有搜索查询都由关键字和运算符组成。 关键字是资产属性的一部分。 可能的关键字可以是分类、术语表术语、资产说明或资产名称。 关键字可以只是要匹配的属性的一部分。 使用下面列出的关键字和运算符来确保 Azure Purview 返回正在查找的资产。 

下面是可用于构成搜索查询的运算符。 可以在单个查询中根据需要多次组合运算符。

| 操作员 | 定义 | 示例 |
| -------- | ---------- | ------- |
| OR | 指定资产必须至少具有这两个关键字中的一个关键字。 必须全部大写。 空格也是 OR 运算符。  | 查询 `hive OR database` 将返回包含“hive”和/或“database”的资产。 |
| AND | 指定资产必须同时具有两个关键字。 必须全部大写 | 查询 `hive AND database` 将返回同时包含“hive”和“database”的资产。 |
| NOT | 指定资产不能包含 NOT 子句右边的关键字 | 查询 `hive NOT database` 将返回包含“hive”但不包含“database”的资产。 |
| () | 将一组关键字和运算符组合在一起。 组合使用多个运算符时，括号指定运算顺序。 | 查询 `hive AND (database OR warehouse)` 将返回包含“hive”以及“database”和/或“warehouse”的资产。 |
| "" | 指定短语中查询必须匹配的确切内容。 | 查询 `"hive database"` 将返回其属性中包含“hive database”短语的资产 |
| * | 匹配一个或多个字符的通配符。 不能是关键字的第一个字符。 | 查询 `dat*` 将返回含有以“dat”开头（例如“data”或“database”）的属性的资产。 |
| ? | 匹配单个字符的通配符。 不能是关键字的第一个字符 | 查询 `dat?` 将返回含有以“dat”开头并且由四个字母组成（例如“date”或“data”）的属性的资产。 |

> [!Note]
> 始终全部以大写字母指定布尔运算符（AND、OR、NOT）  。 否则，大小写无关紧要，额外的空格也无关紧要。

## <a name="next-steps"></a>后续步骤

- [如何创建、导入和导出术语表术语](how-to-create-import-export-glossary.md)
- [如何管理业务术语表的术语模板](how-to-manage-term-templates.md)
