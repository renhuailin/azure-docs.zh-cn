---
title: 排查 Synapse Studio 的问题
description: 排查 Synapse Studio 的问题
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 16608f77971c3c19836d8f956512f28f945d3667
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109809051"
---
# <a name="synapse-studio-troubleshooting"></a>Synapse Studio 故障排除

此故障排除指南提供了有关开具网络连接问题的支持票证时提供哪些信息的说明。 有了正确的信息，我们就可以更快地解决问题。

## <a name="serverless-sql-pool-service-connectivity-issue"></a>无服务器 SQL 池服务连接问题

### <a name="symptom-1"></a>症状 1

“无服务器 SQL 池”选项在“连接到”下拉列表中灰显。

![症状 1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>症状 2

使用“无服务器 SQL 池”运行查询会出现“无法建立与服务器的连接”这一错误消息。

![症状 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>疑难解答步骤

> [!NOTE] 
>    以下故障排除步骤适用于 Chromium Edge 和 Chrome。 你可以使用其他浏览器（如 FireFox）执行相同的故障排除步骤，但是“开发人员工具”窗口的布局可能与此 TSG 中的屏幕截图不同。 如果可能，请勿使用传统 Edge 进行故障排除，因为在某些情况下它可能会显示不正确的信息。

打开“诊断信息”面板，选择“下载诊断”按钮。 保留下载的信息用于报告错误。 可以复制“会话 ID”，并在开具支持票证时附加该 ID。

![诊断信息](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

若要开始进行故障排除，请重试你在 Synapse Studio 中执行的操作。

- 对于症状 1，在“SQL 脚本”选项卡的“使用数据库”下拉列表的右侧选择“刷新”按钮，然后检查是否可以看到“无服务器 SQL 池”。
- 对于症状 2，请尝试再次运行查询以查看其是否成功执行。

如果问题仍然存在，请在浏览器中按 F12 打开“开发人员工具”(DevTools)。

在“开发人员工具”窗口中，切换到“网络”面板。 如有必要，请在“网络”面板中选择工具栏上的“清除”按钮。
确保已选中“网络”面板中的“禁用缓存”。

重试在 Azure Synapse Studio 中执行的操作。 你可能会在“开发人员工具”的“网络”列表中看到新的项。 请注意当前系统时间以在支持票证中提供。

![网络 - 面板 1](media/troubleshooting-synapse-studio/network-panel.png)

查找其 URL 列与以下模式匹配的项：

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

其中 [A] 是你的工作区名称，“-ondemand”可以是“-sqlod”，而 [B] 应该是数据库名称，例如“master” 。 应最多有两个项目具有相同的 URL 值，但方法值不同；OPTIONS 和 POST。 检查这两项在状态列下是否有“200”或“20x”，其中“x”可以是任何一个位的数字。

如果其中任何一个不是“20x”并且：

- 状态以“（失败）”开头，可以加宽“状态”列，或将指针悬停在状态文本上以查看完整文本。 开具支持票证时，应包含文本和/或屏幕截图。

    ![状态文本](media/troubleshooting-synapse-studio/status-text.png)

    - 如果看到 ERR_NAME_NOT_RESOLVED，并且在 10 分钟内创建了工作区，请等待 10 分钟，然后重试以查看问题是否仍然存在。
    - 如果看到 ERR_INTERNET_DISCONNECTED 或 ERR_NETWORK_CHANGED，则表明电脑网络连接有问题。 请检查你的网络连接，然后重试该操作。
    - 如果看到 ERR_CONNECTION_RESET，ERR_SSL_PROTOCOL_ERROR 或其他包含“SSL”的错误代码，则表明你的本地 SSL 配置有问题，或者你的网络管理员已阻止对无服务器 SQL 池服务器的访问。 开具支持票证并在描述中附上错误代码。
    - 如果看到 ERR_NETWORK_ACCESS_DENIED，则可能需要与管理员联系，以检查你的本地防火墙策略是否阻止了对 *.database.windows.net 域或远程端口 1443 的访问。
    - （可选）立即在另一台计算机和/或不同网络环境上尝试相同的操作，以排除电脑上的网络配置问题。

- 状态为“40x”、“50x”或其他数字，选择相应项可查看详细信息。 应会在右侧看到项详细信息。 找到“响应头”部分；然后检查是否存在名为“access-control-allow-origin”的项目。 如果存在，请检查它是否具有以下值之一：

    - `*`（单个星号）
    - https://web.azuresynapse.net/ （或浏览器地址栏中的文本开头的其他值）

如果响应头包含上述值之一，则意味着应已收集失败信息。 你可以根据需要开具支持票证，并可以选择附加项详细信息的屏幕截图。

如果看不到标头，或者标头没有上面列出的值之一，请在开具票证时附加项详细信息的屏幕截图。

 
![项详细信息](media/troubleshooting-synapse-studio/item-details.png)
 
如果上述步骤未能解决问题，可能需要开具支持票证。 提交支持票证时，请包含在本指南开头下载的“会话 ID”或“诊断信息”。

报告问题时，可以选择在“开发人员工具”中截取“控制台”选项卡的屏幕截图，并将其附加到支持票证上。 滚动内容，并在需要时截取多个屏幕截图以捕获整个消息。

![开发人员工具控制台](media/troubleshooting-synapse-studio/developer-tool-console.png)

如果要附加屏幕截图，请提供截取屏幕截图的时间（或估计的时间范围）。 这将有助于我们调查问题。

某些浏览器支持在“控制台”选项卡中显示时间戳。对于 Chromium Edge/Chrome，请在“开发人员工具”中打开“设置”对话框，然后在“首选项”选项卡中选中“显示时间戳”。

![开发人员工具控制台设置](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![显示时间戳](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>后续步骤
如果前面的步骤不能帮助你解决问题，请[创建支持票证](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
