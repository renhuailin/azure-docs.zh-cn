---
title: 操作说明：浏览数据目录
description: 本文概述了如何基于资产类型来浏览 Azure Purview 数据目录。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695057"
---
# <a name="browse-the-azure-purview-data-catalog"></a>浏览 Azure Purview 数据目录

本文介绍如何使用数据源分层命名空间来发现 Azure Purview 数据目录中的数据。

## <a name="browse-experience"></a>浏览体验

数据使用者可以使用资源管理器视图对每个数据源使用熟悉的分层命名空间来发现数据。 在注册并扫描了数据源之后，数据映射会提取有关该数据源的结构（分层命名空间）的信息。 此信息用于构建数据发现的浏览体验。

例如，可以在 Azure Data Lake Storage Gen 2 中名为“Dimensions”的文件夹下轻松找到名为“DateDimension”的数据集 。 可以使用“按资产类型浏览”体验来导航到 ADLS Gen 2 存储帐户，然后浏览到“服务”>“容器”>“文件夹”，以访问特定的“Dimensions”文件夹，然后查看“DateDimension”表 。

将会为每个相应的数据源提供具有分层命名空间的原生浏览体验。

## <a name="browse-the-data-catalog-by-asset-type"></a>按资产类型浏览数据目录

1. 可以通过在主页上选择“按资产类型浏览”来浏览数据资产。

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview 主页" border="true":::

1. 在“浏览资产类型”页上，磁贴是按数据源分类的。 若要进一步浏览每个数据源中的资产，请选择相应的磁贴。

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="“浏览资产类型”页" border="true":::

1. 在下一页上会列出所选数据类型下最高级别的资产。 请选取这些资产中的某一个，以进一步浏览其内容。

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="特定于资产类型的浏览页。显示的示例是 Azure 存储帐户" border="true":::

1. 资源管理器视图将会打开。 请通过选择左侧面板上的某个资产开始浏览。 子资产将会在该页面的右侧面板上列出。

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="资源管理器视图" border="true":::

1. 若要查看资产的详细信息，请选择名称或最右侧的省略号按钮。

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="选择省略号按钮以查看资产详细信息页" border="true":::

## <a name="view-related-data-assets"></a>查看相关数据资产

当你位于资产详细信息页时，还可以查看其他相关的数据资产。 例如，可以导航到 DateDimension 的父文件夹，以查看“Dimensions”文件夹，甚至可以导航到容器，以查看特定层次结构下的资产 。

1. 从资产详细信息页中选择“相关”选项卡，以查看其他相关资产。

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="启动“相关”选项卡" border="true":::

1. 当前资产的完整层次结构将会列在左侧。

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="层次结构" border="true":::

1. 选择层次结构中的任何级别，以列出该级别下的即时资产。

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="选择不同的层次结构" border="true":::

## <a name="next-steps"></a>后续步骤

- [如何创建、导入和导出术语表术语](how-to-create-import-export-glossary.md)
- [如何管理业务术语表的术语模板](how-to-manage-term-templates.md)
