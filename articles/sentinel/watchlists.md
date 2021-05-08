---
title: 使用 Azure Sentinel 播放列表
description: 本文介绍如何使用 Azure Sentinel 监视列表调查威胁，导入业务数据，创建允许列表和扩充事件数据。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555650"
---
# <a name="use-azure-sentinel-watchlists"></a>使用 Azure Sentinel 播放列表

> [!IMPORTANT]
> 监视列表功能目前处于预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure Sentinel 监视列表允许从外部数据源收集数据，以便关联 Azure Sentinel 环境中的事件。 创建监视列表后，可以在搜索、检测规则、威胁搜寻和响应 playbook 中使用它。 监视列表在 Azure Sentinel 工作区中存储为名称值对并缓存，从而提供最佳查询性能和低延迟。

使用监视列表的常见方案包括：

- 通过从 CSV 文件快速导入 IP 地址、文件哈希和其他数据，调查威胁并迅速响应事件。 导入后，可以在警报规则、威胁搜寻、工作簿、笔记本和常规查询中使用监视列表名称值对进行联接和筛选。

- 导入业务数据用作监视列表。 例如，导入对系统具有特权访问权限或访问权限已终止的员工的用户列表，然后使用监视列表创建允许和拒绝列表来检测或阻止这些用户登录网络。

- 缓解警报疲劳。 创建允许列表可抑制一组用户的警报（例如授权 IP 地址中的用户执行通常会触发警报的任务），并防止良性事件成为警报。

- 扩充事件数据。 使用监视列表可以通过派生自外部数据源的名称值组合来扩充事件数据。

## <a name="create-a-new-watchlist"></a>创建新的监视列表

1. 在 Azure 门户中，导航到“Azure Sentinel” > “配置” > “监视列表”，然后选择“新增”。

    > [!div class="mx-imgBorder"]
    > ![新建监视列表](./media/watchlists/sentinel-watchlist-new.png)

1. 在“常规”页中，提供监视列表的名称、说明和别名，然后选择“下一步”。

    > [!div class="mx-imgBorder"]
    > ![监视列表“常规”页](./media/watchlists/sentinel-watchlist-general.png)

1. 在“源”页中，选择数据集类型，上传文件，然后选择“下一步”。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="监视列表“源”页" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 上传的文件大小目前限制为最大 3.8 MB。

1. 预览信息，验证是否正确，然后选择“创建”。

    > [!div class="mx-imgBorder"]
    > ![监视列表预览页](./media/watchlists/sentinel-watchlist-review.png)

    创建监视列表后，将显示一条通知。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="监视列表成功创建通知" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>在查询中使用监视列表

1. 在 Azure 门户中，导航到“Azure Sentinel” > “配置” > “监视列表”，选择要使用的监视列表，然后选择“在日志分析中查看”。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="在查询中使用监视列表" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. 将针对查询自动提取监视列表中的项并显示在“结果”选项卡中。下面的示例显示了“ServerName”和“IpAddress”字段的提取结果。 

    > [!NOTE]
    > 查询 UI 和计划警报中将忽略查询的时间戳。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="使用监视列表字段进行的查询" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. 你可以将监视列表视为可供联接和查找的表，根据监视列表中的数据查询任何表中的数据。

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="将监视列表作为查找对象进行的查询":::

## <a name="use-watchlists-in-analytics-rules"></a>在分析规则中使用监视列表

若要在分析规则中使用监视列表，请在 Azure 门户中导航到“Azure Sentinel” > “配置” > “分析”，并在查询中使用 `_GetWatchlist('<watchlist>')` 函数创建规则。

1. 在此示例中，创建一个名为“ipwatchlist”的监视列表，其中包含以下值：

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="列出用于监视列表的四个项":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="创建包含四个项的监视列表":::

1. 接下来，创建分析规则。  在此示例中，监视列表中只包括来自 IP 地址的事件：

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="在分析规则中使用监视列表":::

## <a name="view-list-of-watchlists-aliases"></a>查看监视列表别名列表

若要获取监视列表别名的列表，请在 Azure 门户中导航到“Azure Sentinel” > “常规” > “日志”，并运行以下查询：`_GetWatchlistAlias`。 你可以在“结果”选项卡中查看别名的列表。

> [!div class="mx-imgBorder"]
> ![列出监视列表](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>后续步骤
本文档介绍了如何使用 Azure Sentinel 中的监视列表来扩充数据并改进调查。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
