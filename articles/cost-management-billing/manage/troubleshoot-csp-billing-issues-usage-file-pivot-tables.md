---
title: 通过使用情况文件数据透视表排查 Azure CSP 计费问题
description: 本文帮助你使用从 CSV 使用情况文件创建的数据透视表排查 Azure 云解决方案提供商 (CSP) 计费问题。
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 2532b02247a85d180d7b1cccd504838b83a8f93f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591836"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>通过使用情况文件数据透视表排查 CSP 计费问题

本文帮助你使用合作伙伴中心对帐（使用情况）文件中的数据透视表排查云解决方案提供商 (CSP) 计费问题。 Azure 使用情况文件包含所有 Azure 使用情况和使用量信息。 文件中的信息可帮助你了解：

- 了解如何使用和应用了 Azure 预留
- 核对 Azure 成本管理中的信息和帐单
- 对成本高峰进行故障排除
- 计算服务级别协议的退款金额

利用使用情况文件中的信息，可以更好地了解使用情况问题并对其进行诊断。 使用情况文件以逗号分隔 (CSV) 格式生成。 由于使用情况文件可能是大型 CSV 文件，因此在 Excel 等电子表格应用程序中作为数据透视表打开它们，可以更轻松地进行操作和查看。 本文中的示例使用 Excel，但你可以使用所需的任何电子表格应用程序。

只有计费管理员和全局管理员有权下载对帐文件。 有关详细信息，请参阅[了解如何读取合作伙伴中心对帐文件中的明细项目](/partner-center/use-the-reconciliation-files)。

## <a name="get-the-data-and-format-it"></a>获取数据并对其进行格式化

由于 Azure 使用文件采用 CSV 格式，因此需要准备数据以在 Excel 中使用。 使用以下步骤将数据格式化为表。

1. 按照[查找帐单](/partner-center/read-your-bill#find-your-bill)中的说明下载使用情况文件。
1. 在 Excel 中打开文件。
1. 未格式化的数据类似于以下示例。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="显示 Excel 中未格式化数据的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. 选择表中的第一个字段，即 PartnerID。
1. 按 Ctrl+Shift+向下键，然后按 Ctrl+Shift+向右键，选择表中的所有信息。
1. 在顶部菜单中，选择“插入” > “表” 。 在“创建表”框中，选择“表包含标题”，然后选择“确定” 。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="显示“创建表”对话框的示例" :::
1. 在顶部菜单中，选择“插入” > “数据透视表”，然后选择“确定”  。 该操作会在文件中创建一个新工作表，并转到工作表右侧的数据透视表区域。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="显示“数据透视表字段”区域的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

“数据透视表字段”区域是拖放区域。 继续执行下一部分以创建数据透视表。

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>创建数据透视表以按资源查看 Azure 成本

在本部分中，你将创建一个数据透视表，可在其中对总体 Azure 使用情况进行故障排除。 示例表可帮助你调查哪项服务消耗的资源最多。 你也可以查看产生的成本最大的资源以及服务的收费方式。

1. 在“数据透视表字段”区域中，将“服务名称”和“资源”拖到“行”区域  。 将“资源”置于“服务名称”下 。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="显示行中的服务名称和资源的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 接下来，将“税后总计”置于“值”区域 。 你还可以改为使用“已使用数量”列来获取关于使用单位和交易的信息。 例如 GB 和小时。 或者事务，而不是采用各种货币单位（如 USD、EUR 和 INR）的成本。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="显示添加到数据透视表字段的列的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. 现在，你有一个用于一般化使用量调查的仪表板。 可以使用数据透视表中的筛选选项来筛选特定服务。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="显示行标签的数据透视表筛选器选项的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    若要筛选数据透视表中的第二级（例如资源），请在表中选择第二级的项。
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="显示“选择”字段的筛选器选项的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. 对于其他筛选器，你可以将“订阅 ID”和“客户公司名称”添加到“筛选器”区域并选择所需的范围  。

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>创建数据透视表以按日期查看 Azure 使用情况

在本部分中，你将创建一个数据透视表，可在该表中根据已使用量和日期对总体 Azure 使用情况进行故障排除。 按日期和服务来识别计费高峰是很有用的。 你也可以查看产生的成本最大的资源以及服务的收费方式。

你的对帐文件有两个表。 一个在顶部（主表），另一个在文档底部。 第二个表包含很多相同的信息，但不包括定价或成本详细信息。 它包含使用日期和已使用量。

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="显示具有两个数据表的对帐文件的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. 按照[获取数据并设置其格式](#get-the-data-and-format-it)部分中的步骤创建一个 Excel 表，其中包含对帐文件底部的信息。
1. 表准备就绪并且你有一个数据透视表工作表时，请按“创建数据透视表以按资源查看 Azure 成本”部分的步骤来准备仪表板。 不使用“税后总计”，而是将“已使用量”置于“值”区域 。
1. 将“使用日期”添加到列部分。 数据透视表应类似于下面的示例。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="显示最终的数据透视表字段的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. 现在就有了一个按日期显示使用情况的仪表板。 选择“+”符号即可展开每个月。

仪表板以 GB、小时和传输次数等为单位显示已使用的量。

若要查看每天的价格，可以将“资源 GUID”添加到“行”区域 。 在上表中，添加资源的单价（定价）。 用“定价”乘以“已使用量”来计算税前费用 。 金额应匹配。

某些资源（服务）已按已使用量分级定价。 例如，对于有些资源，使用的前 100 GB 价格较高；后面使用的量价格较低。 手动计算成本时，请记住分级定价。

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>创建数据透视表以查看特定资源的成本

单项资源可能会产生多项费用，对应于不同的服务。 例如，虚拟机可能会产生计算、OS 许可、带宽（数据传输）、RI 使用情况和快照存储费用。 要查看特定资源的总体使用情况时，以下步骤会引导你创建一个仪表板，让你可以通过使用情况文件查看总体使用情况。

对帐文件不包含特定于资源的详细信息。 因此，使用聚合的使用情况文件。 请与 [Azure 计费支持](https://go.microsoft.com/fwlink/?linkid=2083458)联系，让他们为你提供订阅的聚合使用情况文件。 聚合文件是在订阅级别生成的。 未格式化的数据类似于以下示例。

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="显示未设置格式的聚合使用情况文件的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

该文件包含以下列。

- “UsageStart”和“UsageEnd”- 每个明细项目的日期（每使用单位） 。 例如，每天。
- **MeteredResourceID** - 在 Azure 中，它对应于计量 ID。
- **Properties** - 包含实例 ID（资源名称）和其他详细信息，如位置。
- **Quantity** - 对帐文件中的已使用量。

1. 选择表中的第一个字段，即 PartnerID。  
1. 按 Ctrl+Shift+向下键，然后按 Ctrl+Shift+向右键，选择表中的所有信息。
1. 在顶部菜单中，选择“插入” > “表” 。 在“创建表”框中，选择“表包含标题”，然后选择“确定” 。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="显示“创建表”对话框的示例" :::
1. 在顶部菜单中，选择“插入” > “数据透视表”，然后选择“确定”  。 该操作会在文件中创建一个新工作表，并转到工作表右侧的数据透视表区域。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="显示对帐文件的数据透视表字段区域的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. 接下来，将“MeteredResourceID”添加到“行”区域，将“Quantity”添加到“值”   。 结果显示总体使用情况信息。 有关更多详细信息，请将“UsageEndDateTime”置于“列”区域中 。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="显示总体使用情况信息的示例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. 若要查看整个报表，请在“MeteredResourceID”下将“Properties”添加到“行”  。 它显示一个完整的使用情况仪表板。
1. 若要按特定资源筛选，请将“Properties”添加到“筛选器”区域，然后选择所需的使用情况 。 可以使用“搜索”来查找资源名称。
    若要查看资源的成本，请找到总使用量并用该值乘以定价。 每个资源 GUID (MeteredResourceID) 都有特定的定价。 如果一个资源使用多个 MeteredResourceID，必须记下每个 ID 的总值。

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>后续步骤

- [面向合作伙伴的 Azure 成本管理入门](../costs/get-started-partners.md)。