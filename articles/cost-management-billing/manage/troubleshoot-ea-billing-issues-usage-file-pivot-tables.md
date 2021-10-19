---
title: 通过使用情况文件数据透视表排查 Azure EA 计费问题
description: 本文帮助你使用从 CSV 使用情况文件创建的数据透视表排查企业协议 (EA) 计费问题。
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 4a54c1da5f8525736d4befc56a715759eceae8b8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710967"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>通过使用情况文件数据透视表排查 EA 计费问题

本文帮助你使用使用情况文件中的数据透视表排查 EA 计费问题。 Azure 使用情况文件包含所有 Azure 使用情况和使用量信息。 文件中的信息可帮助你了解：

- 了解如何使用和应用了 Azure 预留
- 核对成本管理中的信息和帐单
- 对成本高峰进行故障排除
- 计算服务级别协议的退款金额

利用使用情况文件中的信息，可以更好地了解使用情况问题并对其进行诊断。 使用情况文件以逗号分隔 (CSV) 格式生成。 由于使用情况文件可能是大型 CSV 文件，因此在 Excel 等电子表格应用程序中作为数据透视表打开它们，可以更轻松地进行操作和查看。 本文中的示例使用 Excel，但你可以使用所需的任何电子表格应用程序。

只有 EA 管理员、帐户所有者和部门管理员才有权下载使用情况文件。

## <a name="get-the-data-and-format-it"></a>获取数据并对其进行格式化

由于 Azure 使用文件采用 CSV 格式，因此需要准备数据以在 Excel 中使用。 使用以下步骤将数据格式化为表。

1. 按照[下载 EA 客户的使用情况数据](../understand/download-azure-daily-usage.md)中的说明，下载包含“所有费用”（使用情况和购买）文件的使用情况详细信息版本 2。
1. 在 Excel 中打开文件。
1. 未格式化的数据类似于以下示例。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="显示 Excel 中未格式化数据的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. 选择表中的第一个字段，该字段包含第一列标题“计费帐户 ID”。
1. 按 Ctrl+Shift+向下键，然后按 Ctrl+Shift+向右键，选择表中的所有信息。
1. 在顶部菜单中，选择“插入” > “表” 。 在“创建表”框中，选择“表包含标题”，然后选择“确定” 。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="显示“创建表”对话框的示例" :::
1. 在顶部菜单中，选择“插入” > “数据透视表”，然后选择“确定”  。 该操作将在文件中创建一个新工作表。 这会带你转到工作表右侧的数据透视表区域。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="显示“数据透视表字段”区域的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

“数据透视表字段”区域是拖放区域。 继续执行下一部分以创建数据透视表。

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>创建数据透视表以按资源查看 Azure 成本

在本部分中，你将创建一个数据透视表，可在其中对总体 Azure 使用情况进行故障排除。 示例表可帮助你调查哪项服务消耗的资源最多。 或者，你可以查看成本最高的资源以及服务的计费方式。

1. 在“数据透视表字段”区域中，将“计量类别”和“产品”拖动到“行”部分  。 将“产品”置于“计量类别”之下 。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="显示“计量类别”和“产品”位于“行”中的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 接下来，将“成本”列添加到“值”部分 。 你还可以改为使用“已使用数量”列来获取关于使用单位和交易的信息。 例如 GB 和小时。 或者事务，而不是采用各种货币单位（如 USD、EUR 和 INR）的成本。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="显示添加到数据透视表字段的列的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. 现在，你有一个用于一般化使用量调查的仪表板。 可以使用数据透视表中的筛选选项来筛选特定服务。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="显示行标签的数据透视表筛选器选项的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    若要筛选数据透视表中的第二级（例如资源），请在表中选择第二级的项。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="显示“选择”字段的筛选器选项的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. 将“ResourceID”列拖动到“行”区域的“产品”下，以便按资源查看每项服务的成本  。 若要查看详细的定价信息，请查看组织的“单价”，并在价格列表的第一列中搜索“产品”。
1. 将“日期”添加到“列”区域，查看产品的每日消耗 。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="显示在列区域放置日期的位置的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. 使用每月对应列的 + 号展开和折叠该月份。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="显示 + 号的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    将“成本”和“数量”列添加到“值”区域是可选操作  。 当“日期”列位于数据透视表的“列”部分中时，这样做会为每月和每天下方的每个数据部分创建两个列。
1. 对于其他筛选器，可以将“订阅 ID”、“部门”、“资源组”、“标记”或“成本中心”列添加到“筛选器”区域，然后选择所需的项。

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>创建数据透视表以查看特定资源的成本

单项资源可能会产生多项费用，对应于不同的服务。 例如，虚拟机可能会产生计算、OS 许可、带宽（数据传输）、RI 使用情况和快照存储费用。 要查看特定资源的总体使用情况时，以下步骤会引导你创建一个仪表板，让你可以通过使用情况文件查看总体使用情况。

1. 在右侧菜单中，将“ResourceID”拖动到数据透视表菜单的“筛选”部分 。
1. 选择要查看其成本的资源。 在“搜索”框中键入内容以查找资源名称。
1. 将“计量类别”和“产品”添加到“行”部分 。 将“产品”置于“计量类别”之下 。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="显示“计量类别”在数据透视表字段区域中的放置位置的示例" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. 接下来，将“成本”列添加到“值”部分 。 你还可以改为使用“已使用数量”列来获取关于使用单位和交易的信息。 例如 GB 和小时。 或者事务，而不是采用各种货币单位（如 USD、EUR 和 INR）的成本。 现在，你有了一个仪表板，其中显示资源使用的所有服务。
1. 将“日期”列添加到“列”部分 。 它将显示每日使用量。
1. 你可以使用每月对应列中的 + 符号来展开和减少显示的内容。  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="显示 + 号的示例" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

[!INCLUDE [Troubleshoot usage spikes](../../../includes/cost-management-billing-troubleshoot-usage-spikes.md)]

## <a name="next-steps"></a>后续步骤

- [使用成本分析探究和分析成本](../costs/quick-acm-cost-analysis.md)。