---
title: 使用存储资源管理器查看知识存储
titleSuffix: Azure Cognitive Search
description: 使用 Azure 门户的存储资源管理器查看和分析 Azure 认知搜索知识存储。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: d87ec3fa05e1740b5cba7b4230894f1df822ac1f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784605"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>使用存储资源管理器查看知识存储

[知识存储](knowledge-store-concept-intro.md)是由 Azure 认知搜索创建并保存到 Azure 存储的内容。 本文介绍如何使用 Azure 门户中的存储资源管理器来查看知识存储的内容。

## <a name="prerequisites"></a>先决条件

以 [Azure 门户](knowledge-store-create-portal.md)中创建或者使用 [REST API](knowledge-store-create-rest.md) 创建的现有知识存储开始。 门户和 REST 演练在 Azure 表存储中创建知识存储。

## <a name="start-storage-explorer"></a>启动存储资源管理器

1. 在 Azure 门户中，[打开用于创建知识存储的存储帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. 在存储帐户的左侧导航窗格中，选择“存储资源管理器”。

## <a name="edit-and-query-tables"></a>编辑和查询表

1. 展开“表”列表以显示创建知识存储时创建的 Azure 表投影列表。 如果使用快速入门或 REST 文章创建了知识存储，这些表将包含与欧洲酒店的客户评论相关的内容。

1. 从该列表中选择一个表。

   ![在存储资源管理器中查看表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在存储资源管理器中查看表")

1. 若要更改表中的数据类型、属性名称或单个数据值，请单击“编辑”。

   ![在存储资源管理器中编辑表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "在存储资源管理器中编辑表")

1. 若要运行查询，请选择命令栏上的“查询”并输入条件。

   ![在存储资源管理器中查询表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "在存储资源管理器中查询表")

在存储资源管理器中，使用[支持的查询语法](/rest/api/storageservices/Querying-Tables-and-Entities)，一次只能查询一个表。 若要跨表进行查询，请考虑改为使用 Power BI。

## <a name="next-steps"></a>后续步骤

将此知识存储连接到 Power BI 以生成包含多个表的可视化效果。

> [!div class="nextstepaction"]
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)
