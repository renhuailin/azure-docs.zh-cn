---
title: 使用存储资源管理器查看知识存储
titleSuffix: Azure Cognitive Search
description: 使用 Azure 门户的存储资源管理器查看和分析 Azure 认知搜索知识存储。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: ee6e17e6fe52125d75f2782b1fa77215045787a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740211"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>使用存储资源管理器查看知识存储

[知识存储](knowledge-store-concept-intro.md)由技能集创建并保存到 Azure 存储。 本文介绍如何使用 Azure 门户中的存储资源管理器来查看知识存储的内容。

## <a name="prerequisites"></a>先决条件

+ 在 [Azure 门户](knowledge-store-create-portal.md)或 [Postman 和 REST API](knowledge-store-create-rest.md) 中创建知识存储。

+ 还需要具有知识存储的 Azure 存储帐户的名称，以及从 Azure 门户获得的其访问密钥。

## <a name="start-storage-explorer"></a>启动存储资源管理器

1. 在 Azure 门户中，[打开用于创建知识存储的存储帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. 在存储帐户的左侧导航窗格中，单击“存储资源管理器”。

## <a name="view-edit-and-query-tables"></a>查看、编辑和查询表

门户和 REST 演练在表存储中创建知识存储。

1. 展开“表”列表以显示创建知识存储时创建的 Azure 表投影列表。 这些表应包含与酒店评论相关的内容。

1. 选择任何表可查看已扩充的数据，包括关键短语和情绪评分。

   ![在存储资源管理器中查看表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在存储资源管理器中查看表")

1. 若要更改任何表值的数据类型或更改表中的各个值，请单击“编辑”。 更改一个表行中任何列的数据类型时，它将应用于所有行。

   ![在存储资源管理器中编辑表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "在存储资源管理器中编辑表")

1. 若要运行查询，请单击命令栏上的“查询”并输入条件。  

   ![在存储资源管理器中查询表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "在存储资源管理器中查询表")

## <a name="next-steps"></a>后续步骤

将此知识存储连接到 Power BI 以进行更深入的分析，或继续编写代码，使用 REST API 和 Postman 创建不同的知识存储。

> [!div class="nextstepaction"]
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)
