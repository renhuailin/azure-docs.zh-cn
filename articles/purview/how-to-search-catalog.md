---
title: 如何：搜索数据目录
description: 本文提供了有关如何搜索数据目录的概述。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551713"
---
# <a name="search-the-azure-purview-data-catalog"></a>搜索 Azure 监控范围数据目录

本文介绍如何使用 Azure 监控范围数据目录中的各种搜索功能。

## <a name="search-the-catalog-for-assets"></a>在目录中搜索资产

执行资产搜索的步骤如下：

1. 通过选择 "**搜索目录** [" 打开 "资产搜索" 对话框](#open-the-asset-search-dialog)。
1. [输入搜索词](#enter-search-terms) 以查找其特征与条款相匹配的资产。
1. [设置快速筛选器](#set-quick-filters) 以缩小搜索范围。
1. [启动搜索](#start-the-search) ，并跳到搜索结果。

无论是在输入搜索词之前还是之后设置快速筛选器都不重要。

如果没有搜索词并且没有筛选器，搜索结果将包括所有资产。

### <a name="open-the-asset-search-dialog"></a>打开 "资产搜索" 对话框

通过选择 " **搜索目录**" 打开 "资产搜索" 对话框。

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="&quot;搜索目录&quot; 下面有一个包含搜索筛选器的左窗格，还有一个包含最新搜索的右窗格。" border="true":::

"搜索" 对话框显示快速筛选器、搜索历史记录和最近访问过的资产列表。

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="搜索列表位于右侧窗格中的 &quot;搜索目录&quot; 下。" border="true":::

### <a name="enter-search-terms"></a>输入搜索词

在 **搜索目录** 中输入一个或多个搜索词。 键入时，将在 **最近** 的搜索中列出匹配的搜索词， **搜索建议** 中列出了建议匹配的搜索词，匹配的数据资产在 **资产建议** 中列出。

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="显示在 &quot;搜索目录&quot; 框中输入的搜索结果的屏幕截图":::

搜索结果仅包含具有一个或多个匹配搜索词的特征的资产。 这些特征包括资产名称、资产类型、分类和联系人。

#### <a name="types-of-search-criteria"></a>搜索条件的类型

Azure 监控范围支持以下类型的搜索条件。

> [!Note]
> 请始终指定布尔运算符， (**和**、 **或**， **而不** 是全部大写的) 。 否则，不区分大小写，也不会产生额外的空间。

- **hive**：查找包含 **hive** 的文档。
- **hive 数据库**：查找只包含 **hive 数据库** 的文档。
- **HIVE 或 database**：查找包含 **hive** 和/或 **数据库** 的文档。
- **HIVE 和数据库**， **hive && 数据库**：查找同时包含 **hive** 和 **数据库** 的文档。
- **HIVE 和 (数据库或仓库)**：查找包含 **hive** 和 **/或****仓库** 的文档。
- **HIVE NOT database**：查找包含 **hive** 但不包含 **数据库** 的文档。
- **hiv**：查找包含以 " **hiv**" 开头的单词的文档。 例如， **hiv**、 **hive**、 **hivbar** ( * 是匹配任意数量) 字符的通配符。

### <a name="set-quick-filters"></a>设置快速筛选器

搜索结果列表基于你在 **搜索目录** 中输入的搜索词，以及你为快速筛选器选择的值。

快速筛选器将搜索结果列表限制为具有所选特性值的资产。 筛选器有一个下拉列表和一个文本框。 下拉列表显示 *当前* 搜索结果中的特性值。 列表中每个值旁是具有该值的当前搜索结果中的资产数量的计数。 如果从列表中选择一个值，搜索结果将限于具有该值的资产。 只能选择一个值。

用于构成下拉列表的当前搜索结果由确定：

- 在 **搜索目录** 中输入的搜索词。 
- 在快速筛选器中选择的值。

下面是 "资产类型" 快速筛选器的示例。

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="资产类型快速筛选器示例。" border="true":::

您可以在文本框中输入文本，以将下拉列表中的值限制为与文本匹配或部分匹配的值。 有关使用文本框的示例，请参阅 [搜索快速筛选器：按资产类型筛选](#search-quick-filter-filter-by-asset-type)和 [搜索快速筛选器：按分类筛选](#search-quick-filter-filter-by-classification)。

#### <a name="search-quick-filter-filter-by-asset-type"></a>搜索快速筛选器：按资产类型筛选

若要按资产类型进行筛选，请使用 **资产类型** 快速筛选器。 下拉列表显示在当前搜索结果中找到的资产类型，由搜索词和快速筛选器确定。 对于每种类型，将显示该类型的资产数。

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="将突出显示 &quot;资产类型快速筛选器&quot;。其中显示了资产类型以及每个资产类型的计数。" border="true":::

选择资产类型可将搜索结果限制为该类型的资产。 只能选择一种类型。

若要仅显示名称与字符串匹配的资产类型，请在文本框中输入字符串。 例如，若要仅显示名称中包含 **sql** 的资产类型，请输入 **sql**。

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="&quot;快速筛选器&quot; 窗格具有 &quot;资产类型&quot; 的 sql。包含 sql 的资产列表显示了三个条目。" border="true":::

选择资产类型可将搜索结果限制为该类型的资产。 您只能选择一种类型。

#### <a name="search-quick-filter-filter-by-classification"></a>搜索快速筛选器：按分类筛选

若要按资产分类进行筛选，请使用 **分类** 快速筛选器。 下拉列表显示了已分配给当前搜索结果中的一个或多个资产的分类，由搜索词和快速筛选器确定。 对于每个分类，将显示分配给分类的资产的数量。

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="分类快速筛选器已突出显示。" border="true":::

选择一个分类，将搜索结果限制为分配了分类的资产。 只能选择一个分类。

若要仅显示名称与字符串匹配的分类，请在文本框中输入字符串。 例如，若要仅显示名称中包含 **数字** 的分类，请输入 " **数字**"。

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="在 &quot;快速筛选器&quot; 窗格中，分类为 &quot;bank&quot;，所列分类全部包含该值。" border="true":::

选择一个分类，将搜索结果限制为已分配该分类的资产。 不能选择多个分类。

#### <a name="search-quick-filter-filter-by-contacts"></a>搜索快速筛选器：按联系人筛选

*联系人* 是指作为所有者或专家分配给资产的人员。 查看资产详细信息时，" **联系人** " 选项卡上将显示 "联系人"。

可以通过两种方式搜索分配有特定联系人的资产。

- 在 **搜索目录** 中输入全部或部分联系人姓名，然后执行搜索。 搜索结果将包括具有名称与搜索词匹配的联系人的资产。
- 选择 **联系人** 快速筛选器中感兴趣的联系人，然后执行搜索。

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="&quot;快速筛选器&quot; 窗格中的 &quot;人员&quot; 的值为 &quot;darren&quot;。建议窗格中有三个建议。" border="true":::

## <a name="search-example"></a>搜索示例

让我们看一个假设示例，了解搜索词和快速筛选器如何交互以确定搜索结果。 具体而言，我们将监视资产类型 " **Azure Blob 存储**" 的计数。

- 我们会执行第一次搜索，但不会输入搜索词，也不会在快速筛选器中选择任何值。 搜索将查找目录中的所有资产。 搜索结果列表和 **资产类型** 快速筛选器显示：

    - 搜索结果列表包含164230资产，这是目录中的所有资产。
    - " **资产类型** " 下拉列表中包含43个条目。 这些是目录中的所有资产类型。 由于每个资产都是一个且仅有一个类型，43资产类型的总数为164230。
    - " **Azure Blob 存储** 资产类型" 是 " **资产类型** 快速筛选器" 下拉列表中的第一个条目。 首先按计数和最大值排序，因此， **Azure Blob 存储** 是最常用的资产类型。 其计数为118174。

- 现在，将 **parquet** 输入 **搜索目录** 中并执行其他搜索。 搜索结果只包括其特征与 **parquet** 匹配的资产。 这会减少所有计数，如下所示：

    - 搜索结果列表中包含493个资产。 目录中只有493的164230资产的特征与 "parquet" 匹配。
    - " **资产类型** " 下拉列表包含15个条目。 493资产中的每一个都属于这15种类型之一，15种类型的计数之和是493。
    - 有456类型的 **Azure Blob 存储** 资产。 其他 37 (493 减去 456) 资产是其他14种类型之一。

- 现在，我们看看不同快速筛选器的下拉列表， **分类**：

    - 在搜索结果列表中，493资产有12个分类。 493资产的计数不到493，因为可将任意数量的分类分配给资产。
    - **人员的名称** 分类分配到36资产，而不是任何其他分类。

- 选择 **人员的名称** 分类。 搜索结果列表将按预期方式下降到36资产，因为 **人员姓名** 的计数为36。 这些结果都不属于 **Azure Blob 存储** 类型。

我们可以得出这样的结论：其类型为 " **Azure Blob 存储** "、与 " **parquet**" 匹配且具有 **人员姓名** 分类的资产。

## <a name="start-the-search"></a>开始搜索

当你搜索时，你在 **搜索目录** 中输入的搜索词会与资产特征匹配。 这些特征包括名称、类型、分类和联系人。 具有匹配特性的资产会显示在搜索结果列表中，除非通过快速筛选器进行排除。

输入搜索词并设置快速筛选器后，请通过以下方式之一来启动搜索：

- 若要根据输入的条款进行搜索，请选择 "搜索" 图标 (:::image type="icon" source="./media/how-to-search-catalog/search-icon.png":::) ，按 **enter**，或选择 " **查看搜索结果**"。
- 若要使用以前搜索中的字词进行搜索，请从 **最近的搜索** 中选择它们。
- 若要使用建议的字词进行搜索，请从 **搜索建议** 中选择它们。

从 **资产建议** 中选择资产，以直接前往资产的详细信息页。 不执行任何搜索。

建议和用户搜索的结果列表可能略有不同。 建议列表中的结果基于模糊匹配，而用户启动的搜索结果基于完全匹配。

搜索时，将显示 " **搜索结果** " 页，并列出搜索找到的资产。

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="显示 contoso 搜索值搜索结果的屏幕截图。":::

若要查看资产详细信息，请选择资产名称。

使用 "搜索结果" 页底部的控件导航到其他搜索结果页。

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="显示如何在搜索结果页中导航的屏幕截图。":::

### <a name="sort-search-results"></a>对搜索结果进行排序

使用 " **排序依据** " 按 **相关性** 或 **名称** 对搜索结果进行排序。

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="显示如何对搜索结果进行排序的屏幕截图。在此示例中，&quot;排序依据&quot; 下拉列表设置为 &quot;关联&quot;。":::

### <a name="search-results-dynamic-filters"></a>搜索结果动态筛选器

"**搜索结果**" 页上的 "**筛选器**" 窗格具有筛选器，可在 "搜索结果" 列表中提供对资产的动态筛选。 筛选是动态的，可以根据筛选器选择显示其他筛选器。

动态筛选器的下拉列表中的每个值都有一个复选框。 使用这些复选框可以根据需要对多个值进行筛选。

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>搜索结果动态筛选器：按资产类型筛选

如果在 " **资产类型** " 下拉列表中选择资产类型，将显示动态筛选器，它为你显示了缩小搜索结果的其他方法。 筛选器根据所选的资产类型而有所不同。 例如，如果选择 " **AZURE SQL 数据库**"，则会显示 "服务器"、"数据库" 和 "架构" 的动态筛选器。 这些筛选器中的值来自所选类型的搜索结果中的资产。

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="&quot;Azure SQL 数据库筛选器&quot; 项是所选的唯一 &quot;资产类型&quot; 项。还将突出显示该资产类型的搜索结果。" border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>搜索结果动态筛选器：按分类筛选

**分类** 列表中的每个分类都适用于 "搜索结果" 列表中的至少一个项目。 选择一个或多个分类以将搜索结果缩小到所选分类的资产。

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="将突出显示 &quot;搜索结果&quot; 的分类筛选器。" border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>编辑和删除搜索结果筛选器

若要删除筛选器，请清除该筛选器名称旁边的复选框。

### <a name="recently-accessed-assets"></a>最近访问的资产

展开的搜索框中的 " **最近访问** " 部分显示最近访问过的资产（如果有）。

- 选择 "**最近访问**" 部分中的 "**查看全部**"，查看最近访问过的资产的完整列表。

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="显示展开的搜索框中的 &quot;最近访问&quot; 部分的屏幕截图。":::

   此时会显示最近访问过的资产列表。

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="显示最近访问的资产列表的屏幕截图。":::

- 若要按名称筛选，请在 " **按名称筛选**" 中输入搜索字符串。

- 若要从列表中删除项目，请选择它们的复选框，然后选择 " **删除**"。

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="显示如何从最近访问过的资产列表中删除项目的屏幕截图。":::

- 若要清除整个列表，请选择 " **清除**"。

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="显示如何清除最近访问的资产列表的屏幕截图":::

### <a name="search-assets"></a>搜索资产

除了 **Home** 以外，许多页面的顶部都有 " **搜索资产** " 框。 例如，下面是 "资产详细信息" 页面，突出显示了 **搜索资产** 。

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="显示突出显示搜索资产的资产详细信息页的屏幕截图":::

选择 "**搜索资产**" 以启动搜索框，如您 **在家****搜索目录** 中获取的搜索框，具有相同的功能。

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="显示展开的搜索资产框的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- [如何创建、导入和导出词汇表术语](how-to-create-import-export-glossary.md)
- [如何管理业务术语表的术语模板](how-to-manage-term-templates.md)
