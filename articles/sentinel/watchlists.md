---
title: 使用 Azure Sentinel 播放列表
description: 本文介绍如何使用 Azure Sentinel 监视列表创建允许列表/阻止列表，扩充事件数据，并协助调查威胁。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 07/11/2021
ms.openlocfilehash: be35be9211ce9d183ab19d768d504798b7150a33
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179372"
---
# <a name="use-azure-sentinel-watchlists"></a>使用 Azure Sentinel 播放列表

Azure Sentinel 监视列表允许从外部数据源收集数据，以便关联 Azure Sentinel 环境中的事件。 创建后，可在搜索、检测规则、威胁搜寻和响应 playbook 中使用监视列表。 监视列表以名称/值对的形式存储在 Azure Sentinel 工作区中，并进行缓存来实现最佳查询性能和低延迟。

> [!IMPORTANT]
> 请注意，这些功能目前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

使用监视列表的常见方案包括：

- 通过从 CSV 文件快速导入 IP 地址、文件哈希和其他数据，调查威胁并迅速响应事件。 导入后，可在警报规则、威胁搜寻、工作簿、笔记本和常规查询中使用监视列表名称/值对进行联接和筛选。

- 导入业务数据用作监视列表。 例如，导入对系统具有特权访问权限或访问权限已终止的员工的用户列表，然后使用监视列表创建允许列表和阻止列表来检测或阻止这些用户登录网络。

- 缓解警报疲劳。 创建允许列表可抑制一组用户的警报（例如授权 IP 地址中的用户执行通常会触发警报的任务），并防止良性事件成为警报。

- 扩充事件数据。 使用监视列表可通过派生自外部数据源的名称/值对组合来扩充事件数据。

> [!NOTE]
> - 应限于对引用数据使用监视列表，因为它们不是针对大型数据卷设计的。
>
> - 单个工作区中所有监视列表中的“活动监视列表项总数”当前限制为 1 千万个。 已删除的监视列表项不会算在此总数内。 如果你需要引用大型数据卷的功能，请考虑改用[自定义日志](../azure-monitor/agents/data-sources-custom-logs.md)引入它们。
>
> - 只能从同一工作区内引用监视列表。 当前不支持跨工作区和/或 Lighthouse 方案。

## <a name="create-a-new-watchlist"></a>创建新的监视列表

1. 在 Azure 门户中，导航到“Azure Sentinel” > “配置” > “监视列表”，然后选择“+ 新增”   。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="新建监视列表" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. 在“常规”页中，提供监视列表的名称、说明和别名，然后选择“下一步：源” 。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="监视列表“常规”页":::

1. 在“源”页面上，选择数据集类型（目前仅 CSV 可选），在数据文件的标头行之前输入行数，然后通过以下两种方式之一选择要上传的文件 ：
    1. 单击“上传文件”框中的“浏览文件”链接，然后选择要上传的数据文件 。
    1. 将数据文件拖放到“上传文件”框。

    向导屏幕中将显示前 50 行结果的预览。

1. 在 SearchKey 字段中，输入监视列表中你希望用作与其他数据的联接或用作常见搜索对象的列的名称。 例如，如果服务器监视列表包含国家/地区名称及其相应的两字母国家/地区代码，并且你希望经常将这些国家/地区代码用于搜索或联接，请使用“代码”列作为 SearchKey。

1. <a name="review-and-create"></a>选择“下一步: 审阅并创建”。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="监视列表“源”页" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 文件上传大小当前限制为不超过 3.8 MB。

1. 查看信息，验证是否正确，等待“验证通过”消息，然后选择“创建”。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="监视列表预览页":::

    创建监视列表后，将显示一条通知。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="监视列表成功创建通知" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="create-a-new-watchlist-using-a-template-public-preview"></a>使用模板（公共预览版）创建新监视列表

1. 从 Azure 门户中，导航到“Azure Sentinel” > “配置” > “监视列表” > “模板(预览版)”。

1. 从列表中选择一个模板并在右侧查看其详细信息，然后选择“从模板创建”以创建监视列表。

    :::image type="content" source="./media/watchlists/create-watchlist-from-template.png" alt-text="从内置模板创建监视列表。" lightbox="./media/watchlists/create-watchlist-from-template.png":::

1. 在“监视列表向导”中继续：

    - 使用监视列表模板时，监视列表的“名称”、“描述”和“监视列表别名”值都为只读。

    - 选择“下载架构”，下载包含所选监视列表模板所需的相关架构的 CSV 文件。

    每个内置监视列表模板都有自己的数据集，这些数据列在附加到模板的 CSV 文件中。 有关详细信息，请参阅[内置监视列表架构](watchlist-schemas.md)

1.  填充 CSV 文件的本地版本，然后将其上传回向导中。

1. 按照[从头创建新监视列表](#review-and-create)中的步骤继续操作，然后根据[查询](#use-watchlists-in-queries)和[分析规则](#use-watchlists-in-analytics-rules)使用监视列表。

## <a name="use-watchlists-in-queries"></a>在查询中使用监视列表

> [!TIP]
> 为获得最佳查询性能，请在查询时使用 SearchKey（表示创建监视列表时定义的字段）作为联接的密钥。 请参阅以下示例。

1. 在 Azure 门户中，导航到“Azure Sentinel” > “配置” > “监视列表”，选择要使用的监视列表，然后选择“在日志分析中查看”。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="在查询中使用监视列表" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png" :::

1. 系统会自动为查询提取监视列表中的项，并将它们显示在“结果”选项卡上。以下示例显示 Name 和 IP Address 字段的提取结果  。 SearchKey 显示为自己的列。

    > [!NOTE]
    > 查询 UI 和计划警报中将忽略查询的时间戳。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="使用监视列表字段进行的查询" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::

1. 你可以将监视列表视为可供联接和查找的表，根据监视列表中的数据查询任何表中的数据。 使用 SearchKey 作为联接的密钥。

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('mywatchlist') 
     on $left.RemoteIPCountry == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="将监视列表作为查找对象进行的查询" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>在分析规则中使用监视列表

> [!TIP]
> 为获得最佳查询性能，请在查询时使用 SearchKey（表示创建监视列表时定义的字段）作为联接的密钥。 请参阅以下示例。

若要在分析规则中使用监视列表，请在 Azure 门户中导航到“Azure Sentinel” > “配置” > “分析”，并在查询中使用 `_GetWatchlist('<watchlist>')` 函数创建规则。

1. 在此示例中，创建一个名为“ipwatchlist”的监视列表，其中包含以下值：

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="列出用于监视列表的四个项":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-other.png" alt-text="创建包含四个项的监视列表":::

1. 接下来，创建分析规则。  在此示例中，监视列表中只包括来自 IP 地址的事件：

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    //Use SearchKey for the best performance
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project SearchKey)
    )
    ```

    :::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="在分析规则中使用监视列表":::

## <a name="view-list-of-watchlists-aliases"></a>查看监视列表别名列表

若要获取监视列表别名的列表，请在 Azure 门户中导航到“Azure Sentinel” > “常规” > “日志”，并运行以下查询：`_GetWatchlistAlias`。 你可以在“结果”选项卡中查看别名的列表。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="列出监视列表" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="manage-your-watchlist-in-the-azure-sentinel-portal"></a>在 Azure Sentinel 门户中管理监视列表

还可以直接从 Azure Sentinel 门户中的“监视列表”边栏选项卡查看、编辑和创建新的监视列表项。

1. 若要编辑监视列表，请导航到“Azure Sentinel”>“配置”>“监视列表”，选择要编辑的监视列表，然后在“详细信息”窗格上选择“编辑监视列表项”。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit.png" alt-text="显示如何编辑监视列表的屏幕截图" lightbox="./media/watchlists/sentinel-watchlist-edit.png":::

1. 若要编辑现有的监视列表项，请标记该监视列表项的复选框，编辑该项，然后选择“保存”。 在出现确认提示时单击“是”。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-change.png" alt-text="显示如何标记和编辑监视列表项的屏幕截图。":::

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-confirm.png" alt-text="确认你所做的更改的屏幕截图。":::

1. 若要向监视列表中添加新项，请在“编辑监视列表项”屏幕上选择“新增”，填写“添加监视列表项”面板中的字段，然后在该面板底部选择“添加”。

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-add.png" alt-text="显示如何向监视列表添加新项的屏幕截图。":::

## <a name="next-steps"></a>后续步骤
本文档介绍了如何使用 Azure Sentinel 中的监视列表来扩充数据并改进调查。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。