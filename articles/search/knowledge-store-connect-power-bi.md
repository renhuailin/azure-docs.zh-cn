---
title: 使用 Power BI 连接知识存储
titleSuffix: Azure Cognitive Search
description: 使用 Power BI 连接 Azure 认知搜索知识存储以进行分析和浏览。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 97a0025437b7f055f9fcf2e7860e926e9693cde6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766910"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>使用 Power BI 连接知识存储

本文介绍如何使用 Power BI Desktop 应用中的 Power Query 来连接和浏览知识存储。 使用模板可以更快地开始操作，也可以从头开始构建自定义仪表板。

由 Azure 存储中的表组成的知识存储最适用于 Power BI。 如果表包含来自相同技能组合和投影组的投影，则可以轻松地“联接”它们来构建包含相关表中的字段的表可视化效果。

遵循本文中的步骤，使用[在 Azure 门户中](knowledge-store-create-portal.md)或通过 [Postman 和 REST API](knowledge-store-create-rest.md) 创建的示例数据和知识库。 

## <a name="connect-to-azure-storage"></a>连接到 Azure 存储

1. 启动 [Power BI Desktop](https://powerbi.microsoft.com/downloads/) 并选择“获取数据”。 

1. 在“获取数据”窗口中，依次选择“Azure”、“Azure 表存储”。  

1. 选择“连接”。

1. 对于“帐户名称或 URL”，请输入你的 Azure 存储帐户名称（系统将为你创建完整 URL）。

1. 如果有提示，请输入存储帐户密钥。

## <a name="set-up-tables"></a>设置表

1. 选中从同一技能组合创建的所有表旁边的复选框，然后选择“加载”。

   ![加载表](media/knowledge-store-connect-power-bi/power-bi-load-tables.png "加载表")

1. 在顶部功能区中，选择“转换数据”以打开“Power Query 编辑器”。

   ![打开 Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "打开 Power Query")

1. 选择“hotelReviewsSsDocument”，然后删除“PartitionKey”、“RowKey”和“Timestamp”列。    

   ![编辑表](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "编辑表")

1. 单击表右上方带有反向箭头的图标，以展开“内容”。 显示列的列表后，选择所有列，并取消选择以“metadata”开头的列。 单击“确定”显示所选列。

   ![扩展内容](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "扩展内容")

1. 单击列左上方的 ABC-123 图标，更改以下列的数据类型。

   + 对于“content.latitude”和“Content.longitude”，请选择“十进制数”。 
   + 对于“Content.reviews_date”和“Content.reviews_dateAdded”，请选择“日期/时间”。 

   ![更改数据类型](media/knowledge-store-connect-power-bi/powerbi-change-type.png "更改数据类型")

1. 选择“hotelReviewsSsPages”，然后重复步骤 9 和 10 删除列并展开“内容”。 

1. 选择“hotelReviewsSsKeyPhrases”，然后重复步骤 9 和 10 删除列并展开“内容”。  对于此表，无需修改数据类型。

1. 在命令栏上，单击“关闭并应用”。

## <a name="check-table-relationships"></a>检查表关系

1. 单击左侧导航窗格中的“模型”磁贴，验证 Power BI 是否显示了所有三个表之间的关系。

   ![验证关系](media/knowledge-store-connect-power-bi/powerbi-relationships.png "验证关系")

1. 双击每个关系，确保“交叉筛选方向”设置为“双向”。   这样，在应用筛选器时，视觉对象将会刷新。

## <a name="build-a-report"></a>生成报表

1. 单击左侧导航窗格上的“报表”磁贴，以通过可视化浏览数据。 对于文本字段，表和卡是有用的可视化效果。

1. 从三个表中的每个表中选择字段，以填充表或卡。

   ![生成一个表报表](media/knowledge-store-connect-power-bi/power-bi-table-report.png "生成表报表")

## <a name="sample-power-bi-template---azure-portal-only"></a>示例 Power BI 模板 - 仅限 Azure 门户

创建[使用 Azure 门户的知识存储](knowledge-store-create-portal.md)时，可以选择在“导入数据”向导的第二页下载 [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates)。 此模板包含多种适用于基于文本的内容的可视化效果，如 WordCloud 和网络导航器。 

在“添加认知技能”页面单击“获取 Power BI 模板”，从其公共 GitHub 位置检索并下载模板 。 向导会修改模板，以适应数据形状，如在向导中指定的知识存储投影中所捕获的一样。 因此，每次运行该向导时，下载的模板会有所不同（假设数据输入和技能选择不同）。

![示例 Azure 认知搜索 Power BI 模板](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "示例 Power BI 模板")

> [!NOTE]
> 该模板是在向导运行到中途时下载的。 必须等到系统在 Azure 表存储中实际创建了知识存储后，才能加以使用。

## <a name="video-introduction"></a>视频简介

有关将 Power BI 与知识库一起使用的演示，请观看以下视频。

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Power BI 报表和仪表板中的表](/power-bi/visuals/power-bi-visualization-tables)