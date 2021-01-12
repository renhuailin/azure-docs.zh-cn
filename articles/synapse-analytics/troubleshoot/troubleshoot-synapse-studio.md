---
title: 排查 Synapse Studio 的问题
description: 排查 Azure Synapse Studio 问题
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116171"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Azure Synapse Studio 故障排除

此故障排除指南提供了有关在解决网络连接问题时提供哪些信息的说明。 有了正确的信息，我们就可以更快地解决问题。

## <a name="serverless-sql-pool-service-connectivity-issue"></a>无服务器 SQL 池服务连接问题

### <a name="symptom-1"></a>症状 1

"无服务器 SQL 池" 选项在 "连接到" 下拉列表中灰显。

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>症状 2

通过 "无服务器 SQL 池" 运行查询会使你 "无法建立与服务器的连接" 错误消息。

![症状2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>疑难解答步骤

> [!NOTE] 
>    以下故障排除步骤适用于 Chromium Edge 和 Chrome。 您可以使用其他浏览器 (如 FireFox) ，使用相同的疑难解答步骤，但 "开发人员工具" 窗口可能与此 TSG 中的屏幕截图具有不同的布局。 如果可能，请不要使用传统边缘进行故障排除，因为在某些情况下可能会显示不准确的信息。

打开 "诊断信息" 面板，然后选择 "下载诊断" 按钮。 保留下载的有关错误报告的信息。 您可以改为复制 "会话 ID"，并在打开支持票证时附加该 ID。

![诊断信息](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

若要开始进行故障排除，请重试在 Azure Synapse Studio 中执行的操作。

- 对于 "症状 1"，选择 "SQL 脚本" 选项卡中 "使用数据库" 下拉列表右侧的 "刷新" 按钮，并检查是否可以看到 "无服务器 SQL 池"。
- 对于症状2，请尝试再次运行查询以查看它是否成功执行。

如果问题仍然存在，请在浏览器中按 F12 打开 "开发人员工具" (DevTools) "。

在 "开发人员工具" 窗口中，切换到 "网络" 面板。 如有必要，请在 "网络" 面板中选择工具栏上的 "清除" 按钮。
请确保选中 "网络" 面板中的 "禁用缓存"。

重试在 Azure Synapse Studio 中执行的操作。 你可能会看到 "开发人员工具" 中的 "网络" 列表中显示的新项。 请注意当前系统时间以在支持票证中提供。

![网络-面板1](media/troubleshooting-synapse-studio/network-panel.png)

查找其 Url 列与以下模式匹配的项：

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

其中，[*A*] 是你的工作区名称，而 "-ondemand" 可能是 "-sqlod"，其中 [*B*] 应为数据库名称，如 "master"。 最多应有两个具有相同 URL 值但不同方法值的项;选项和 POST。 检查这两个项的 "状态" 列中是否有 "200" 或 "20 倍"，其中 "x" 可以是任何单个数字。

如果其中任何一个不是 "20 倍"，则为：

- 状态以 " (failed) " 开头，扩大 "Status" 列或将指针悬停在状态文本上以查看完整的文本。 打开支持票证时，包括文本和/或屏幕截图。

    ![状态文本](media/troubleshooting-synapse-studio/status-text.png)

    - 如果你看到 ERR_NAME_NOT_RESOLVED，并在10分钟内创建了你的工作区，请等待10分钟，然后重试以查看问题是否仍然存在。
    - 如果你看到 ERR_INTERNET_DISCONNECTED 或 ERR_NETWORK_CHANGED，则可能表示你的电脑网络连接有问题。 请检查你的网络连接，然后重试该操作。
    - 如果你看到 ERR_CONNECTION_RESET、ERR_SSL_PROTOCOL_ERROR 或其他包含 "SSL" 的错误代码，则可能表示你的本地 SSL 配置存在问题，或者你的网络管理员已阻止访问无服务器 SQL 池服务器。 打开支持票证并附加描述中的错误代码。
    - 如果你看到 ERR_NETWORK_ACCESS_DENIED，则可能需要与管理员联系，了解你的本地防火墙策略是否已阻止对 *. database.windows.net 域或远程端口1443的访问。
    - 也可以在不同的计算机和/或网络环境上立即尝试相同的操作，以排除计算机上的网络配置问题。

- Status 为 "40 倍"、"50x" 或其他数字，请选择 () 项，查看详细信息。 你应看到右侧的项详细信息。 找到 "响应标头" 部分;然后检查是否存在名为 "访问控制-允许-源" 的项。 如果是这样，请检查它是否具有以下值之一：

    - `*` (单星号) 
    - https://web.azuresynapse.net/ (或浏览器地址栏中的文本以) 开头的其他值

如果响应标头包含上述值之一，则意味着应已收集失败信息。 如果需要，你可以打开支持票证，还可以选择附加项详细信息的屏幕截图。

如果看不到该标头，或标头没有上面列出的值之一，请在打开该票证时附加项详细信息的屏幕截图。

 
![项详细信息](media/troubleshooting-synapse-studio/item-details.png)
 
如果上述步骤无法解决你的问题，你可能需要打开支持票证。 提交支持票证时，请包括本指南开头部分的 "会话 ID" 或 "诊断信息"。

报告问题时，您可以选择在 "开发人员工具" 中获取 "控制台" 选项卡的屏幕截图，并将其附加到支持票证。 滚动内容并在需要时使用多个屏幕截图来捕获整个消息。

![开发人员工具控制台](media/troubleshooting-synapse-studio/developer-tool-console.png)

如果要附加屏幕快照，请在拍摄屏幕截图时，提供时间 (或估计时间范围) 。 这将有助于我们查看问题。

某些浏览器支持在 "控制台" 选项卡中显示时间戳。对于 Chromium Edge/Chrome，请打开 "开发人员工具" 中的 "设置" 对话框，并选中 "首选项" 选项卡中的 "显示时间戳"。

![开发人员工具控制台设置](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![显示时间戳](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>后续步骤
如果前面的步骤不能帮助解决问题，请 [创建支持票证](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)