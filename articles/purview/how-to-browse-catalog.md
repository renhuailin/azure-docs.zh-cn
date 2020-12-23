---
title: 如何：浏览数据目录
description: 本文提供了有关如何基于资产类型浏览 Azure 监控范围数据目录的概述。
author: hrasheed-msft
ms.author: hrasheed
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: b8cdbbc29472ae10920c347dde308c352bf0b68a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551643"
---
# <a name="browse-the-azure-purview-data-catalog"></a>浏览 Azure 监控范围数据目录

本文介绍如何使用数据源分层命名空间发现 Azure 监控范围数据目录中的数据。

## <a name="browse-experience"></a>浏览体验

数据使用者可以使用 "资源管理器" 视图对每个数据源使用熟悉的分层命名空间来发现数据。 注册并扫描数据源后，数据映射会提取有关数据源的结构 (分层命名空间) 的信息。 此信息用于构建数据发现的浏览体验。

例如，可以在 Azure Data lake Storage 第2代中名为 "*维度*" 的文件夹下轻松找到名为 *DateDimension* 的数据集。 你可以使用 "按资产类型浏览" 体验导航到 ADLS 第2代存储帐户，然后浏览到服务 > 容器 > 文件夹 (s) 以访问特定的 *维度* 文件夹，然后查看 *DateDimension* 表。

将为相应的每个数据源提供具有分层命名空间的本机浏览体验。

## <a name="browse-the-data-catalog-by-asset-type"></a>按资产类型浏览数据目录

1. 可以通过在主页上选择 " **按资产类型浏览** " 来浏览数据资产。

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="监控范围主页" border="true":::

1. 在 " **浏览资产类型** " 页上，磁贴按数据源分类。 若要进一步浏览每个数据源中的资产，请选择相应的磁贴。

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="&quot;浏览资产类型&quot; 页" border="true":::

1. 在下一页上，列出了所选数据类型下的顶级资产。 选择其中一个资产，进一步浏览其内容。

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="特定于资产类型的浏览页。示例显示了 Azure 存储帐户" border="true":::

1. 资源管理器视图将打开。 通过选择左侧面板上的 "资源" 开始浏览。 子资产将在页面的右面板上列出。

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="资源管理器视图" border="true":::

1. 若要查看资产的详细信息，请选择最右侧的 "名称" 或 "省略号" 按钮。

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="选择省略号按钮以查看资产详细信息页" border="true":::

## <a name="view-related-data-assets"></a>查看相关数据资产

在 "资产详细信息" 页上，你还可以查看其他相关的数据资产。 例如，您可以导航到 *DateDimension* 的父文件夹以查看 " *维度* " 文件夹，甚至可以导航到该容器以查看特定层次结构下的资产。

1. 从 "资产详细信息" 页中，选择 " **相关** " 选项卡以查看其他相关资产。

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="启动相关的选项卡" border="true":::

1. 当前资产的完整层次结构将列在左侧。

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="层次结构" border="true":::

1. 选择层次结构中的任何级别以列出该级别下的即时资产。

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="选择不同的层次结构" border="true":::

## <a name="next-steps"></a>后续步骤

- [如何创建、导入和导出词汇表术语](how-to-create-import-export-glossary.md)
- [如何管理业务术语表的术语模板](how-to-manage-term-templates.md)
