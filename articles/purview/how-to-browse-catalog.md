---
title: 操作说明：浏览数据目录
description: 本文概述了如何按资产类型浏览 Azure Purview 数据目录
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: ab5cb2856343d4fdcfcbd6c3c88d45889c191d2d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456774"
---
# <a name="browse-the-azure-purview-data-catalog"></a>浏览 Azure Purview 数据目录

如果数据使用者知道自己需要查找哪些内容，则搜索数据目录就是一种很好的数据发现手段，不过，用户往往并不确切地知道他们的数据资产是如何构建的。 Azure Purview 数据目录提供了浏览体验，使用户能够按集合或遍历目录中每个数据源的层次结构来浏览可用的数据。

若要访问浏览体验，请从数据目录主页中选择“浏览资产”。

:::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview 主页" border="true":::

## <a name="browse-by-collection"></a>按集合浏览

按集合浏览可以浏览你充当其数据读取者或管护者的不同集合。

> [!NOTE]
> 你只会看到你有权访问的集合。 有关详细信息，请参阅[创建和管理集合](how-to-create-and-manage-collections.md)。

:::image type="content" source="media/how-to-browse-catalog/browse-by-collection.png" alt-text="显示“按集合浏览”页的屏幕截图" border="true":::

选择一个集合后，你将根据搜索中提供的分面和筛选器获取该集合中的资产列表。 由于一个集合可能包含数千个资产，浏览功能会使用 Purview 搜索相关性引擎将最重要的资产提升到最前面。

:::image type="content" source="media/how-to-browse-catalog/browse-collection-results.png" alt-text="显示按集合浏览后的结果的屏幕截图" border="true":::

对于某些批注，可以单击省略号以在 AND 条件或 OR 条件之间选择。 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="显示如何在 AND 或 OR 条件之间进行选择的屏幕截图" border="true":::

如果所选集合不包含你要查找的数据，你可以轻松导航到相关集合，或返回并查看整个集合树。

:::image type="content" source="media/how-to-browse-catalog/browse-collection-navigation.png" alt-text="显示如何在集合之间导航的屏幕截图" border="true":::

找到要查找的资产后，可以选择它来查看更多详细信息，例如架构、世系和详细的分类列表。 若要详细了解资产详细信息页，请参阅[管理目录资产](catalog-asset-details.md)。

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="显示资产详细信息页的屏幕截图" border="true":::

## <a name="browse-by-source-type"></a>按源类型浏览

按源类型浏览使数据使用者能够使用资源管理器视图来浏览数据源的层次结构。 选择源类型以查看扫描的源的列表。

例如，可以在 Azure Data Lake Storage Gen 2 中名为“Dimensions”的文件夹下轻松找到名为“DateDimension”的数据集 。 可以使用“按源类型浏览”体验来导航到 ADLS Gen 2 存储帐户，然后浏览到“服务”>“容器”>“文件夹”，以访问特定的“Dimensions”文件夹，然后查看“DateDimension”表 。

将会为每个相应的数据源提供具有分层命名空间的原生浏览体验。

> [!NOTE]
> 成功完成范围扫描后，可能会延迟一段时间才会在浏览体验中显示新扫描的资产。


1. 在“按源类型浏览”页上，磁贴是按数据源分类的。 若要进一步浏览每个数据源中的资产，请选择相应的磁贴。

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.png" alt-text="“浏览资产类型”页" border="true":::

   > [!TIP]
   > 某些磁贴是对数据源集合进行的分组。 例如，“Azure 存储帐户”磁贴包含所有 Azure Blob 存储帐户和 Azure Data Lake Storage Gen2 帐户。 “Azure SQL Server”磁贴会显示包含 Azure SQL 数据库的 Azure SQL Server 资产，以及引入到目录中的 Azure 专用 SQL 池实例。 

1. 在下一页上会列出所选数据类型下最高级别的资产。 请选取这些资产中的某一个，以进一步浏览其内容。 例如，在选择“Azure SQL 数据库”后，你将看到包含数据目录中的资产的数据库列表。

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.png" alt-text="Azure SQL 数据库浏览页" border="true":::

1. 资源管理器视图将会打开。 请通过选择左侧面板上的某个资产开始浏览。 子资产将会在该页面的右侧面板上列出。

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.png" alt-text="资源管理器视图" border="true":::

1. 若要查看资产的详细信息，请选择名称或最右侧的省略号按钮。

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.png" alt-text="选择省略号按钮以查看资产详细信息页" border="true":::

## <a name="next-steps"></a>后续步骤

- [如何创建、导入和导出术语表术语](how-to-create-import-export-glossary.md)
- [如何管理业务术语表的术语模板](how-to-manage-term-templates.md)
- [如何搜索 Purview 数据目录](how-to-search-catalog.md)
