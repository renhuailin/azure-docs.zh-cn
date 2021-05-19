---
title: 通过 Power BI 直观显示日志和指标
description: 通过 Power BI 直观显示 Azure 认知搜索日志和指标。
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: fbbeb861e50abfd393b416ddc46ff147fffb7b8e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581630"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>通过 Power BI 直观显示 Azure 认知搜索日志和指标

[Azure 认知搜索](./search-what-is-azure-search.md)可以将操作日志和服务指标发送到 Azure 存储帐户，然后你可以在 Power BI 中直观显示这些日志和指标。 本文介绍了相关步骤以及如何使用 Power BI 模板应用来直观显示数据。 该模板可帮助你获取有关你的搜索服务的详细见解，包括有关查询、索引编制、操作和服务指标的信息。

可以在 [Power BI 应用市场](https://appsource.microsoft.com/marketplace/apps)中找到 Power BI 模板应用“Azure 认知搜索: 分析日志和指标”。

## <a name="set-up-the-app"></a>设置应用

1. 为搜索服务启用指标和资源日志记录：

    1. 创建或标识可以将日志存档到其中的现有 [Azure 存储帐户](../storage/common/storage-account-create.md)
    1. 在 Azure 门户中导航到你的 Azure 认知搜索服务
    1. 在左侧列中的“监视”部分，选择“诊断设置”

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="屏幕截图显示了如何在 Azure 认知搜索服务的“监视”部分选择“诊断设置”。" border="false":::

    1. 选择“+ 添加诊断设置”
    1. 选中“存档到存储帐户”，提供你的存储帐户信息，并选中“OperationLogs”和“AllMetrics”

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="屏幕截图显示了如何在诊断设置页中针对指标和资源日志记录进行选择。":::
    1. 选择“保存”

1. 启用日志记录后，使用搜索服务开始生成日志和指标。 需要长达一个小时，包含这些日志的容器才会出现在 Blob 存储中。 你会看到用于搜索流量日志的“insights-logs-operationlogs”容器和用于指标的“insights-metrics-pt1m”容器。

1. 在 [Power BI 应用市场](https://appsource.microsoft.com/marketplace/apps)中找到 Azure 认知搜索 Power BI 应用，并将其安装到新的工作区或现有的工作区。 该应用称为“Azure 认知搜索: 分析日志和指标”。

1. 安装该应用后，从 Power BI 的应用列表中选择该应用。

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="屏幕截图显示了要从应用列表中选择的 Azure 认知搜索应用。":::

1. 选择“连接”以连接你的数据

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="屏幕截图显示了如何连接到 Azure 认知搜索应用中的数据。":::

1. 输入包含你的日志和指标的存储帐户的名称。 默认情况下，该应用将查看过去 10 天的数据，但你可以使用“天数”参数更改此值。

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="屏幕截图显示了如何在“连接到 Azure 认知搜索”页中输入存储帐户名称和要查询的天数。":::

1. 选择“密钥”作为身份验证方法，并提供你的存储帐户密钥。 选择“专用”作为隐私级别。 单击“登录”开始加载程序。

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="屏幕截图显示了如何在“连接到 Azure 认知搜索”页中输入身份验证方法、帐户密钥和隐私级别。":::

1. 等待数据刷新。 这可能需要一些时间，具体取决于你拥有的数据量。 你可以根据下面的指示器查看数据是否仍在刷新。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="屏幕截图显示了如何读取数据刷新页上的信息。":::

1. 在数据刷新完成后，选择“Azure 认知搜索报表”以查看报表。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="屏幕截图显示了如何在数据刷新页上选择“Azure 认知搜索报表”。":::

1. 确保在打开报表后刷新页面，使其在打开时显示你的数据。

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="屏幕截图显示了 Azure 认知搜索 Power BI 报表。":::

## <a name="modify-app-parameters"></a>修改应用参数

如果要可视化其他存储帐户中的数据或更改要查询的数据天数，请按照以下步骤更改“天数”和“StorageAccount”参数。

1. 导航到你的 Power BI 应用，找到你的 Azure 认知搜索应用，然后选择“编辑应用”按钮以查看工作区。

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="屏幕截图显示了如何选择 Azure 认知搜索应用的“编辑应用”按钮。":::

1. 从“数据集”选项中选择“设置”。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="屏幕截图显示了如何从“Azure 认知搜索数据集”选项中选择“设置”。":::

1. 在“数据集”选项卡中，更改参数值并选择“应用”。 如果连接出现问题，请在同一页面上更新数据源凭据。

1. 导航回工作区，并从“数据集”选项中选择“立即刷新”。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="屏幕截图显示了如何从“Azure 认知搜索数据集”选项中选择“立即刷新”。":::

1. 打开报表以查看更新后的数据。 你可能还需要刷新报表以查看最新数据。

## <a name="troubleshooting-report-issues"></a>故障排除报告问题

如果你发现找不到你的数据，请执行以下故障排除步骤：

1. 打开报表并刷新页面以确保查看最新数据。 报表中有一个用于刷新数据的选项。 请选择此选项以获取最新数据。

1. 确保你提供的存储帐户名称和访问密钥是正确的。 存储帐户名称应当对应于配置了搜索服务日志的帐户。

1. 确认你的存储帐户包含容器“insights-logs-operationlogs”和“insights-metrics-pt1m”，并且每个容器都有数据。 日志和指标将位于多层文件夹中。

1. 查看数据集是否仍在刷新。 上面的步骤 8 中显示了刷新状态指示器。 如果仍在刷新，请等待刷新完成，然后再打开并刷新报表。

## <a name="next-steps"></a>后续步骤

+ [监视搜索操作和活动](search-monitor-usage.md)
+ [什么是 Power BI？](/power-bi/fundamentals/power-bi-overview)
+ [Power BI 服务中设计器的基本概念](/power-bi/service-basic-concepts)