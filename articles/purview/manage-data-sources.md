---
title: '在 Azure 监控范围中管理数据源 (预览) '
description: 了解如何注册新数据源、管理数据源的集合，以及如何在 Azure 监控范围 (预览) 中查看源。
author: mamccrea
ms.author: mamccrea
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 382c79cf77cb22d3cad1af41d6daa662fb00df4c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781158"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>在 Azure 监控范围中管理数据源 (预览) 

本文介绍如何在 Azure 监控范围中注册新数据源、管理数据源集合以及查看 Azure 中的源 (预览) 。 Azure 监控范围支持以下数据源：

* 本地 SQL server
* Azure Data Lake Storage Gen1 
* Azure Data Lake Storage Gen2
* Azure Blob 存储
* Azure 数据资源管理器
* Azure SQL DB
* Azure SQL DB 托管实例
* Azure Synapse Analytics（以前称为 SQL DW）
* Azure Cosmos DB
* Power BI
* Teradata（预览版）

## <a name="register-a-new-source"></a>注册新的源

使用以下步骤注册新的源。

1. 打开监控范围 Studio，然后选择 " **注册源** " 磁贴。

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure 监控范围 Studio":::

1. 选择 " **注册**"，然后选择源类型。 此示例使用 Azure Blob 存储。 选择“继续”。 

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="在 &quot;注册源&quot; 页中选择数据源类型":::

1. 在 " **注册源** " 页上填写表单。 为源选择一个名称，并输入相关信息。 如果选择 " **从 Azure 订阅** " 作为帐户选择方法，则订阅中的源将显示在下拉列表中。 或者，您也可以手动输入您的源信息。

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="数据源信息窗体":::

1. 选择“完成”  。

## <a name="view-sources"></a>查看源

可以在 Azure 监控范围 Studio 的 " **源** " 选项卡上查看所有已注册的源。 视图类型有两种：地图视图和列表视图。

### <a name="map-view"></a>地图视图

在 "地图" 视图中，可以看到所有源和集合。 在下图中，有一个 Azure Blob 存储源。 可以从每个源磁贴中编辑源、启动新扫描或查看源详细信息。

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Azure 监控范围数据源地图视图":::

### <a name="list-view"></a>列表视图

在列表视图中，可以看到源的可排序列表。 将鼠标悬停在要编辑的选项的源上方，开始新扫描或删除。

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Azure 监控范围数据源列表视图":::

## <a name="manage-collections"></a>管理集合

您可以将您的数据源分组到集合中。 若要创建新集合，请在 Azure 监控范围 Studio 的 "*源*" 页上选择 " **+ 新建集合**"。 为集合指定一个名称，并选择 " *无* " 作为父级。 新集合将显示在 "地图" 视图中。

若要将源添加到集合中，请选择 "在源上 **编辑** 铅笔"，然后从 " **选择集合** " 下拉菜单中选择一个集合。

若要创建集合的层次结构，请将较高级别的集合分配为较低级别集合的父级。 在下图中， *Fabrikam* 是 *财务* 集合的父级，其中包含一个 Azure Blob 存储数据源。 通过单击附加到 "级别" 之间箭头的圆圈，可以折叠或展开集合。

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Azure 监控范围 Studio 中的集合的层次结构":::

您可以通过为父项选择 " *无* " 从层次结构中删除源。 Unparented 源在地图视图的虚线框中分组，无箭头将它们链接到父级。

## <a name="next-steps"></a>后续步骤

了解如何注册和扫描各种数据源：

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI 租户](register-scan-power-bi-tenant.md)
* [Azure SQL 数据库](register-scan-azure-sql-database.md)
