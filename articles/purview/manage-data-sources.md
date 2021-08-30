---
title: 在 Azure Purview（预览版）中管理数据源
description: 了解如何在 Azure Purview（预览版）中注册新数据源、管理数据源的集合以及查看源。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: c1e60ae792921ef4e218918f093001ee9947975d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724267"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>在 Azure Purview（预览版）中管理数据源

本文介绍如何在 Azure Purview（预览版）中注册新数据源、管理数据源的集合以及查看源

## <a name="register-a-new-source"></a>注册新源

请使用以下步骤注册新源。

1. 打开 Purview Studio，选择“注册源”磁贴。

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure Purview Studio":::

1. 选择“注册”，然后选择源类型。 本示例使用 Azure Blob 存储。 选择“继续”。

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="在“注册源”页中选择数据源类型":::

2. 在“注册源”页上填写表单。 为源选择一个名称，并输入相关信息。 如果选择“从 Azure 订阅”作为帐户选择方法，随即会以下拉列表的形式显示订阅中的源。 

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="数据源信息表单":::

3. 选择“注册”。

## <a name="view-sources"></a>查看源

可以在 Azure Purview Studio 的“数据映射”选项卡上查看所有已注册的源。 有两种视图类型：地图视图和列表视图。

### <a name="map-view"></a>地图视图

在“地图”视图中，可以看到所有源和集合。 下图中有一个 Azure Blob 存储源。 可以从每个源磁贴中编辑源、启动新扫描或查看源详细信息。

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Azure Purview 数据源地图视图":::

### <a name="list-view"></a>列表视图

在列表视图中，可以看到可排序的源列表。 将鼠标悬停在源上方，可显示编辑、开始新扫描或删除等选项。

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Azure Purview 数据源列表视图":::

## <a name="manage-collections"></a>管理集合

可将数据源归组到集合中。 若要创建新集合，请在 Azure Purview Studio 的“源”页上选择“+新建集合”。 为集合提供一个名称，然后为“父级”选择“无”。 随即地图视图中会显示新集合。

若要将源添加到集合，请选择源上的“编辑”铅笔，然后从“选择集合”下拉菜单中选择一个集合 。

若要创建集合的层次结构，请将高级别的集合分配为低级别集合的父级。 下图中，Fabrikam 是 Finance 集合的父级，后者包含 Azure Blob 存储数据源 。 可以通过单击级别之间的箭头上所附的圆圈来折叠或展开集合。

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Azure Purview Studio 中集合的层次结构":::

可以从层次结构中删除源，方法是为父级选择“无”。 地图视图中将无父级的源归组到虚线框中，且没有箭头将它们链接到父项。

## <a name="next-steps"></a>后续步骤

了解如何注册和扫描各种数据源：

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI 租户](register-scan-power-bi-tenant.md)
* [Azure SQL 数据库](register-scan-azure-sql-database.md)
