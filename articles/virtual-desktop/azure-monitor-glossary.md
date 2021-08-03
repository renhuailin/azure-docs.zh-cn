---
title: Azure Monitor for Windows Virtual Desktop 术语表 - Azure
description: 与 Azure Monitor for Windows Virtual Desktop 相关的术语和概念词汇表。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: dba8ecd9bbeca9c0b48f312d9c6c4ab27b52c9e7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745306"
---
# <a name="azure-monitor-for-azure-virtual-desktop-glossary"></a>Azure Monitor for Windows Virtual Desktop 术语表

本文列出并简要介绍了 Azure Monitor for Windows Virtual Desktop（预览版）的关键术语和概念。

## <a name="alerts"></a>警报

在订阅中配置并归类为 [0 级严重程度](#severity-0-alerts)的任何有效 Azure Monitor 警报都将显示在“概述”页中。 若要了解如何设置警报，请参阅 [Azure Monitor 日志警报](../azure-monitor/alerts/alerts-log.md)。

## <a name="available-sessions"></a>可用会话数

可用会话显示了主机池中的可用会话数。 此服务通过以下方式计算此数字：将虚拟机 (VM) 的数量与每个虚拟机允许的最大会话数相乘，然后减去总会话数。

## <a name="connection-success"></a>连接成功

此项显示连接运行状况。 “连接成功”表示连接可以到达主机，该虚拟机上的堆栈可确认。 连接失败表示着该连接无法到达主机。

## <a name="daily-active-users-dau"></a>每日活动用户 (DAU)

在过去 24 小时内启动会话的用户总数。

## <a name="daily-alerts"></a>每日警报数

每天触发的警报总数。

## <a name="daily-connections-and-reconnections"></a>每日连接数和重新连接数

在过去 24 小时内开始或完成的连接和重新连接的总数。

## <a name="daily-connected-hours"></a>每日连接小时数

在过去 24 小时内用户连接到会话所用的总小时数。

## <a name="diagnostics-and-errors"></a>诊断和错误

如果适用于 Azure Monitor for Azure Virtual Desktop 出现错误或警报，则可分为以下三类：

- 活动类别：此类别是 Azure 虚拟桌面诊断对错误进行分类的方式。 类别包括管理活动、源、连接、主机注册、错误和检查点。 有关这些类别的详细信息，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

- 种类：此类别显示错误的位置。 

     - 标记为“service”或“ServiceError = TRUE”的错误发生在 Azure 虚拟桌面服务中。
     - 标记为“deployment”或“ServiceError = FALSE”的错误发生在 Azure 虚拟桌面服务之外。
     - 若要了解有关 ServiceError 标记的详细信息，请参阅[常见错误情况](diagnostics-role-service.md#common-error-scenarios)。

- 源：该类别提供有关错误发生位置的更具体描述。

     - 诊断：负责监视和报告服务活动的服务角色，使用户能够观察和诊断部署问题。

     - RDBroker：负责协调部署活动、维护对象状态、验证身份验证等内容的服务角色。

     - RDGateway：负责处理最终用户和虚拟机之间的网络连接的服务角色。

     - RDStack：用户可借助安装在 VM 上的软件组件，使 VM 能够与 Azure 虚拟桌面服务进行通信。

     - 客户端：在最终用户计算机上运行的软件，为 Azure 虚拟桌面服务提供接口。 做出选择后，它会显示已发布资源的列表并托管远程桌面连接。

每个诊断问题或错误都包含一条消息，说明发生的错误。 若要详细了解如何排查错误，请参阅[识别和诊断 Azure 虚拟桌面问题](diagnostics-role-service.md)。

## <a name="input-delay"></a>输入延迟

Azure Monitor for Azure Virtual Desktop 中的“输入延迟”表示每个会话的每个进程性能计数器的输入延迟。 在“主机性能” ([aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)) 页面上，我们已将此性能计数器配置为每 30 秒向服务发送一次报告。 这 30 秒的间隔称为“示例”，并在该窗口中报告最坏的情况。 中值和 p95 值反映所有示例中的中值和第 95 个百分点值。

在“输入延迟(按主机)”下，你可以选择一个会话主机行，将页面中的所有其他视觉对象筛选到该主机。 还可以选择进程名称，筛选一段时间内的输入延迟的中值。

我们将延迟分为以下几类：

- 好：低于 150 毫秒。
- 可接受：150-500 毫秒。
- 较差：500-2000 毫秒（低于 2 秒）。
- 差：超过 2000 毫秒（2 秒及以上）。

若要了解输入延迟计数器的工作原理，请参阅[用户输入延迟性能计数器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

##  <a name="monthly-active-users-mau"></a>月度活跃用户 (MAU)

在过去 28 天内启动会话的用户总数。 如果数据的存储时间少于或等于 30 天，则在可用数据时间少于 28 天的期间，你可能会看到低于预期的 MAU 和连接值。

## <a name="performance-counters"></a>性能计数器

性能计数器显示硬件组件、操作系统和应用程序的性能。

下表列出了 Azure Monitor for Azure Virtual Desktop 的建议性能计数器和时间间隔：

|性能计数器名称|时间间隔|
|---|---|
|Logical Disk(C:)\\Avg.磁盘队列长度|30 秒|
|Logical Disk(C:)\\Avg.磁盘秒数/传输|60 秒|
|Logical Disk(C:)\\Current Disk Queue Length|30 秒|
|Memory(\*)\\Available Mbytes|30 秒|
|Memory(\*)\\Page Faults/sec|30 秒|
|Memory(\*)\\Pages/sec|30 秒|
|Memory(\*)\\% Committed Bytes in Use|30 秒|
|PhysicalDisk(\*)\\Avg.磁盘队列长度|30 秒|
|PhysicalDisk(\*)\\Avg.磁盘秒数/读取|30 秒|
|PhysicalDisk(\*)\\Avg.磁盘秒数/传输|30 秒|
|PhysicalDisk(\*)\\Avg.磁盘秒数/写入|30 秒|
|Processor Information(_Total)\\% Processor Time|30 秒|
|Terminal Services(\*)\\Active Sessions|60 秒|
|Terminal Services(\*)\\Inactive Sessions|60 秒|
|Terminal Services(\*)\\Total Sessions|60 秒|
|\*每进程用户输入延迟（\*）\\最大输入延迟|30 秒|
|\*User Input Delay per Session(\*)\\Max Input Delay|30 秒|
|RemoteFX Network(\*)\\Current TCP RTT|30 秒|
|RemoteFX Network(\*)\\Current UDP Bandwidth|30 秒|

若要了解有关如何读取性能计数器的详细信息，请参阅[配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md)。

若要了解有关输入延迟性能计数器的详细信息，请参阅[用户输入延迟性能计数器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

## <a name="potential-connectivity-issues"></a>潜在连接问题

潜在连接问题显示了连接失败率较高的主机、用户、已发布资源以及客户端。 选择“报告者”筛选器后，可以检查这些列中的值来评估问题的严重性：

- 尝试（连接尝试数）
- 资源（已发布应用或桌面数）
- 主机（VM 数）
- 客户端

例如，如果选择“按用户”筛选器，则可以在“尝试”列中查看每个用户的连接尝试。

如果你注意到连接问题跨多个主机、用户、资源或客户端，则该问题可能会影响整个系统。 如果不是，则这是较低优先级的问题。

你还可以选择条目以查看其他信息。 可以查看与此问题相关的主机、资源和客户端版本。 还将显示在连接尝试过程中报告的任何错误。

## <a name="round-trip-time-rtt"></a>往返时间 (RTT)

往返时间 (RTT) 是最终用户的位置与会话主机的 Azure 区域之间的连接往返时间的估算值。 若要查看哪些位置的延迟最大，请在 [Azure 虚拟桌面体验评估器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)中查找所需的位置。

## <a name="session-history"></a>会话历史记录

“会话”项显示所有会话的状态（已连接和已断开连接）。 “空闲会话”仅显示已断开连接的会话。

## <a name="severity-0-alerts"></a>0 级严重程度警报

你需要立即处理的最紧急的项目。 如果未解决这些问题，则可能会导致 Azure 虚拟桌面部署停止工作。

## <a name="time-to-connect"></a>连接时间

连接时间是指用户启动其会话的时间与计为登录到服务的时间之间的时间。 建立新连接通常比重新建立现有连接所需的时间更长。

## <a name="user-report"></a>用户报表

在用户报表页上，你可以查看特定用户的连接历史记录和诊断信息。 每个用户报表都显示了使用模式、用户反馈以及用户在其会话过程中遇到的任何错误。 大多数小问题可以通过用户反馈解决。 如果需要深入了解，还可以筛选有关特定连接 ID 或时间段的信息。

## <a name="users-per-core"></a>每核心用户数

这是每个虚拟机核心中的用户数。 跟踪一段时间内每个核心的最大用户数有助于确定环境是否以高、低或波动的用户数（每个核心）持续运行。 了解有多少用户处于活动状态有助于有效地为环境提供资源并缩放环境。

## <a name="windows-event-logs"></a>Windows 事件日志

Windows 事件日志是在 Windows 虚拟机上的 Log Analytics 代理收集的数据源。 除了由需要监视的应用程序创建的自定义日志，还可以从标准日志，如“系统”和“应用程序”中收集事件。

下表列出了 Azure Monitor for Azure Virtual Desktop 所需的 Azure 事件日志：

|事件名称|事件类型|
|---|---|
|应用程序|“错误”和“警告”|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin|“错误”、“警告”和“信息”。|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|“错误”、“警告”和“信息”。|
|System|“错误”和“警告”|
| Microsoft-FSLogix-Apps/Operational|“错误”、“警告”和“信息”。|
|Microsoft-FSLogix-Apps/Admin|“错误”、“警告”和“信息”。|

若要详细了解 Windows 事件日志，请参阅[ Windows 事件记录属性](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)。

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅[使用 Azure Monitor for Azure Virtual Desktop 监视部署](azure-monitor.md)。
- 若要估计、度量和管控数据存储成本，请参阅[估计 Azure Monitor 成本](azure-monitor-costs.md)。
- 如果遇到问题，请查看[故障排除指南](troubleshoot-azure-monitor.md)，获取帮助并了解已知问题。


你还可以设置 Azure 顾问，以帮助你确定如何解决或阻止常见问题。 有关详细信息，请参阅[结合使用 Azure 顾问与 Azure 虚拟桌面](azure-advisor.md)。

如果你需要帮助或有任何问题，请查看我们的社区资源：

- 可在 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)向社区提出问题或建议。
   
- 若要了解如何留下反馈，请参阅[ Azure 虚拟桌面的故障排除概述、反馈和支持](troubleshoot-set-up-overview.md#report-issues)。

- 还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈
