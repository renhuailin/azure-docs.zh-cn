---
title: 监视 Windows 虚拟桌面预览术语表-Azure
description: 与 Windows 虚拟桌面 Azure Monitor 相关的术语和概念术语表。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 29c49ceb3647964030f53c94276e831dc0f648c7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576616"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>Windows 虚拟桌面 (预览版) 术语表的 Azure Monitor

>[!IMPORTANT]
>Windows 虚拟桌面 Azure Monitor 当前提供公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文列出并简要介绍了与 Windows 虚拟桌面 (preview) Azure Monitor 相关的关键术语和概念。

## <a name="alerts"></a>警报

在订阅上配置并归类为 " [严重性 1](#severity-1-alerts) " 的任何活动 Azure Monitor 警报都将显示在 "概述" 页中。 若要了解如何设置警报，请参阅 [响应带有 Azure Monitor 警报的事件](../azure-monitor/alerts/tutorial-response.md)。

## <a name="available-sessions"></a>可用会话数

可用会话显示了主机池中的可用会话数。 此服务通过将虚拟 (机的数量与每个虚拟机允许的最大会话数相乘) 计算此数字，然后减去总会话数。

## <a name="connection-success"></a>连接成功

此项显示连接运行状况。 "连接成功" 表示连接可以到达主机，如虚拟机上的堆栈所确认。 连接失败表示连接无法连接到主机。

## <a name="daily-active-users-dau"></a>每日活动用户 (DAU) 

在过去24小时内启动会话的用户总数。

## <a name="daily-alerts"></a>每日警报数

最近24小时内触发的 [严重级别1警报](#severity-1-alerts) 总数。

## <a name="daily-connections-and-reconnections"></a>每日连接数和重新连接数

在过去24小时内，连接和重新连接的总数已开始或完成。

## <a name="daily-connected-hours"></a>每日连接小时数

过去24小时内跨用户连接到会话所用的总小时数。

## <a name="diagnostics-and-errors"></a>诊断和错误

如果 Windows 虚拟桌面 Azure Monitor 中出现错误或警报，则按以下三项分类：

- 活动类型：此类别是 Windows 虚拟桌面诊断对错误进行分类的方式。 类别包括管理活动、源、连接、主机注册、错误和检查点。 有关这些类别的详细信息， [请参阅使用 Log Analytics 诊断功能](diagnostics-log-analytics.md)。

- Kind：此类别显示错误的位置。 

     - Windows 虚拟桌面服务中发生的错误标记为 "service" 或 "ServiceError = TRUE"。
     - Windows 虚拟桌面服务外部发生了标记为 "部署" 或标记为 "ServiceError = FALSE" 的错误。
     - 若要了解有关 ServiceError 标记的详细信息，请参阅 [常见错误情况](diagnostics-role-service.md#common-error-scenarios)。

- 源：此类别对发生错误的位置提供更具体的说明。

     - 诊断：负责监视和报告服务活动的服务角色，使用户能够观察和诊断部署问题。

     - RDBroker：负责协调部署活动、维护对象状态、验证身份验证等内容的服务角色。

     - RDGateway：负责处理最终用户和虚拟机之间的网络连接的服务角色。

     - RDStack：一种安装在 Vm 上的软件组件，可用于与 Windows 虚拟桌面服务进行通信。

     - 客户端：在向 Windows 虚拟桌面服务提供接口的最终用户计算机上运行的软件。 进行选择后，它会显示已发布资源的列表并托管远程桌面连接。

每个诊断问题或错误都包含一条消息，说明发生了什么错误。 若要详细了解如何排查错误，请参阅 [识别和诊断 Windows 虚拟桌面问题](diagnostics-role-service.md)。

## <a name="input-delay"></a>输入延迟

Windows 虚拟桌面 Azure Monitor 中的 "输入延迟" 表示每个会话的每个进程性能计数器的输入延迟。 在 <aka.ms/azmonwvdi> 的 "主机性能" 页上，此性能计数器配置为每隔30秒向服务发送一次报告。 这30秒的间隔称为 "示例"，并在该窗口中报告最坏的情况。 中间值和 p95 值反映所有示例中的中间值和95%。

在 " **按主机输入延迟**" 下，您可以选择一个会话主机行来筛选该主机上页面中的所有其他视觉对象。 您还可以选择进程名称来筛选随时间图表的中间输入延迟。

我们将延迟置于以下类别中：

- 好：低于150毫秒。
- 可接受：150-500 毫秒。
- 差： 500-2000 毫秒 (低于2秒) 。
- 错误：超过2000毫秒 (2 秒，向上) 。

若要详细了解输入延迟计数器的工作方式，请参阅 [用户输入延迟性能计数器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

##  <a name="monthly-active-users-mau"></a>月度活跃用户 (MAU)

在过去28天内启动会话的用户总数。 如果在30天或更短时间内存储数据，则可能会在你的可用数据量少于28天的时段内看到 "预期的 MAU" 和 "连接" 值。

## <a name="performance-counters"></a>性能计数器

性能计数器显示硬件组件、操作系统和应用程序的性能。

下表列出了建议用于 Windows 虚拟桌面的性能计数器和时间间隔 Azure Monitor：

|性能计数器名称|时间间隔|
|---|---|
|逻辑磁盘 (C： ) \\ 平均磁盘队列长度|30 秒|
|逻辑磁盘 (C： ) \\ Avg. Disk sec/Transfer|60 秒|
|逻辑磁盘 (C： ) \\ 当前磁盘队列长度|30 秒|
|内存 (\*) \\ 可用兆字节|30 秒|
|内存 (\*) \\ 页错误数/秒|30 秒|
|内存 (\*) \\ Pages/sec|30 秒|
|内存 (\*) \\ 使用中的已提交字节数|30 秒|
|PhysicalDisk (\*) \\ 平均磁盘队列长度|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Sec/Read|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Sec/Transfer|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Sec/Write|30 秒|
|进程 (\*) \\ 处理器时间百分比|20秒|
|进程 (\*) \\ 用户时间百分比|30 秒|
|进程 (\*) \\ 线程计数|30 秒|
|处理 (\*) \\ IO 写入操作数/秒|30 秒|
|处理 (\*) \\ IO 读取操作数/秒|30 秒|
|处理器信息 (_Total) \\ 处理器时间百分比|30 秒|
|终端服务 (\*) \\ 活动会话|60 秒|
|终端服务 (\*) \\ 非活动会话|60 秒|
|终端服务 (\*) \\ 会话总数|60 秒|
|\*每个进程 (的用户输入延迟 \*) \\ 最大输入处理|30 秒|
|\*每个会话 (的用户输入延迟 \*) \\ 最大输入延迟|30 秒|
|RemoteFX 网络 (\*) \\ 当前的 TCP RTT|30 秒|
|RemoteFX 网络 (\*) \\ 当前 UDP 带宽|30 秒|

若要了解有关如何读取性能计数器的详细信息，请参阅 [配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md)。

若要了解有关输入延迟性能计数器的详细信息，请参阅 [用户输入延迟性能计数器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

## <a name="potential-connectivity-issues"></a>潜在连接问题

潜在连接问题显示了主机、用户、已发布资源以及连接失败率较高的客户端。 选择 "报表方式" 筛选器后，可以通过检查这些列中的值来评估问题的严重性：

- 尝试 (连接尝试次数) 
- 资源 (已发布应用或桌面的数量) 
- 承载 (的 Vm 数量) 
- 客户端

例如，如果选择 " **按用户** " 筛选器，则可以在 " **尝试** " 列中查看每个用户的连接尝试。

如果你注意到连接问题跨多个主机、用户、资源或客户端，则问题可能会影响整个系统。 如果不是，则这是较低优先级的问题。

你还可以选择条目以查看其他信息。 你可以查看与此问题相关的主机、资源和客户端版本。 此显示还将显示在连接尝试过程中报告的任何错误。

## <a name="round-trip-time-rtt"></a>RTT (往返时间) 

 (RTT 的往返时间) 是最终用户的位置与 VM 的 Azure 区域之间的连接往返时间估算。 若要查看哪些位置有最大的延迟，请在 [Windows 虚拟桌面体验估计器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)中查找所需的位置。

## <a name="session-history"></a>会话历史记录

" **会话** " 项显示已连接和已断开连接的所有会话的状态。 **空闲会话** 仅显示断开连接的会话。

## <a name="severity-1-alerts"></a>严重性1警报

你需要立即处理的最紧急的项目。 如果未解决这些问题，则可能会导致 Windows 虚拟桌面部署停止工作。

## <a name="time-to-connect"></a>连接时间

连接时间是指用户启动其会话的时间与被计为登录到服务的时间之间的时间。 建立新连接通常比重新建立现有连接需要更长的时间。

## <a name="user-report"></a>用户报表

用户报表页使你可以查看特定用户的连接历史记录和诊断信息。 每个用户报表显示了使用模式、用户反馈以及用户在其会话过程中遇到的任何错误。 最小的问题可以通过用户反馈解决。 如果需要深入了解，还可以筛选有关特定连接 ID 或时间段的信息。

## <a name="users-per-core"></a>每核心用户数

这是每个虚拟机核心中的用户数。 跟踪一段时间内每个核心的最大用户数可帮助你确定环境是否以高、低或更多的用户数（每个核心）持续运行。 了解有多少用户处于活动状态将帮助你有效地资源并缩放环境。

## <a name="windows-events"></a>Windows 事件

Windows 事件日志是在 Windows 虚拟机上 Log Analytics 代理收集的数据源。 你可以从诸如系统和应用程序的标准日志以及需要监视的应用程序创建的自定义日志中收集事件。

下表列出了 Windows 虚拟桌面 Azure Monitor 所需的 Windows 事件：

|事件名称|事件类型|
|---|---|
|应用程序|错误和警告|
|Microsoft-windows-terminalservices-gateway-RemoteConnectionManager/Admin|错误、警告和信息|
|Microsoft-Microsoft-windows-terminalservices-gateway-LocalSessionManager/Operational|错误、警告和信息|
|系统|错误和警告|
| Microsoft FSLogix-应用程序/操作|错误、警告和信息|
|FSLogix/Admin|错误、警告和信息|

若要了解有关 Windows 事件的详细信息，请参阅 [windows 事件记录属性](../azure-monitor/agents/data-sources-windows-events.md)。

## <a name="next-steps"></a>后续步骤

若要开始 Windows 虚拟桌面 Azure Monitor，请查看以下文章：

- [使用 Windows 虚拟桌面 Azure Monitor 来监视部署](azure-monitor.md)
- [Windows 虚拟桌面故障排除的 Azure Monitor](troubleshoot-azure-monitor.md)

你还可以设置 Azure 顾问，以帮助你确定如何解决或阻止常见问题。 有关详细信息， [请参阅将 Azure Advisor 与 Windows 虚拟桌面配合使用](azure-advisor.md)。

如果你需要帮助或有任何问题，请查看我们的社区资源：

- 向 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)的社区提出问题或提出建议。
   
- 若要了解如何留下反馈，请参阅 [Windows 虚拟桌面的故障排除概述、反馈和支持](troubleshoot-set-up-overview.md#report-issues)。

- 你还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 或 [我们的 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)上为 windows 虚拟桌面提供反馈。
