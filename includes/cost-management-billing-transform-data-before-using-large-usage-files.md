---
title: include 文件
description: include 文件
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026207"
---
## <a name="transform-data-before-using-large-usage-files"></a>在使用大型使用情况文件之前转换数据

有时，使用情况或对帐文件太大，无法打开。 或者，你可能只需要一部分信息来解决问题。 例如，你可能只需要特定资源的信息，或者只需要少数服务或资源组的使用量信息。 在创建数据透视表之前，可以转换数据以汇总数据。

1. 在 Excel 中打开一个空白工作簿。
1. 在上面的菜单中，选择“数据” > “从文本/CSV”，选择你的使用情况文件，然后选择“导入”  。
1. 在窗口底部，选择“转换数据”。 新窗口显示数据的摘要。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="示例显示汇总的数据" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. 如果你有 Microsoft 客户协议，请跳过此步骤并继续下一步，因为 MCA 使用情况文件通常将列标题显示在第一行。 通过创建表来准备数据。 删除前几行，只保留标题。 选择“删除行” > “删除前几行” 。  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="示例显示汇总的数据" :::
1. 在“删除前几行”窗口中，输入要删除的前几行的行数。 对于 EA 通常为 2，对于 CSP 通常为 1。 选择“确定”  。
1. 选择“将第一行用作标头”。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="示例显示汇总的数据" :::
    
    表视图显示顶部的列标题。
1. 接下来，添加筛选器。 使用每个列标题右侧的选择器箭头进行筛选。 建议的筛选器包括订阅 ID、服务名称（计量类别）、实例 ID、资源组。 你可以在同一文档中使用多个筛选器。 我们建议你应用所有可能的筛选器来减小文档大小，方便以后的工作。
1. 应用筛选器后，选择“关闭并加载”。  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="示例显示汇总的数据" :::

该文件加载并显示一个包含筛选后的使用情况数据的表。 现在，你可以创建一个新的数据透视表来排查使用情况问题。