---
title: Windows 虚拟桌面诊断日志分析 - Azure
description: 如何将日志分析与 Windows 虚拟桌面诊断功能配合使用。
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 37990cc4322717f090c7a35c62512ba0e1a04293
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576143"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>将 Log Analytics 用于诊断功能

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)。

就像使用许多其他 Azure 服务一样，Windows 虚拟桌面使用 [Azure Monitor](../azure-monitor/overview.md) 进行监视和发出警报。 这样，管理员便可以集中通过一个界面识别问题。 该服务为用户活动和管理活动创建活动日志。 每个活动日志都会归属于以下类别：

- 管理活动：
    - 对使用 API 或 PowerShell 更改 Windows 虚拟桌面对象的尝试是否成功进行跟踪。 例如，是否有人可以使用 PowerShell 成功创建主机池？
- 源：
    - 用户是否可以成功订阅工作区？
    - 用户是否能看到远程桌面客户端中发布的所有资源？
- 连接:
    - 在用户启动和完成到服务的连接时。
- 主机注册：
    - 会话主机是否在连接时成功注册了服务？
- 错误：
    - 用户是否遇到了与特定活动相关的任何问题？ 只要这些信息与活动联接，此功能就可以生成一个跟踪活动数据的表。
- 检查点：
    - 已到达的某个活动生存期的特定步骤。 例如，在某个会话过程中，将某个用户负载均衡到了某个特定主机，然后该用户在某个连接过程中登录了，等等。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 在用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

利用 Azure Monitor，可以分析 Windows 虚拟桌面数据并查看虚拟机 (VM) 性能计数器，所有操作都在同一工具内进行。 本文将介绍如何为 Windows 虚拟桌面环境启用诊断。

>[!NOTE]
>若要了解如何在 Azure 中监视 VM，请参阅[利用 Azure Monitor 监视 Azure 虚拟机](../azure-monitor/vm/monitor-vm-azure.md)。 此外，请确保[查看性能计数器阈值](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds)，以便更好地了解你在会话主机上的用户体验。

## <a name="before-you-get-started"></a>准备工作

你需要创建工作区，然后才能使用 Log Analytics。 为此，请按照以下两篇文章之一的说明进行操作：

- 如果你更喜欢使用 Azure 门户，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。
- 如果你更喜欢 PowerShell，请参阅[使用 Powershell 创建 Log Analytics 工作区](../azure-monitor/logs/powershell-workspace-configuration.md)。

在创建了工作区之后，请按照[将 Windows 计算机连接到 Azure Monitor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) 中的说明获取以下信息：

- 工作区 ID
- 工作区的主密钥

稍后在设置过程中将会需要这些信息。

请确保查看 Azure Monitor 的权限管理，以便让监视和维护你的 Windows 虚拟桌面环境的人员能够进行数据访问。 有关详细信息，请参阅 [Azure Monitor 的角色、权限和安全入门](../azure-monitor/roles-permissions-security.md)。

## <a name="push-diagnostics-data-to-your-workspace"></a>将诊断数据推送到工作区

可以将诊断数据从 Windows 虚拟桌面对象推送到工作区的 Log Analytics 中。 在首次创建对象时，可以立即设置此功能。

若要为新对象设置 Log Analytics，请执行以下操作：

1. 登录到 Azure 门户并转到 Windows 虚拟桌面。

2. 导航到要捕获其日志和事件的对象（例如主机池、应用组或工作区）。

3. 在屏幕左侧的菜单中选择“诊断设置”。

4. 在屏幕右侧出现的菜单中选择“添加诊断设置”。

    “诊断设置”页中显示的选项将会因所编辑的对象类型而异。

    例如，在为应用组启用诊断时，你会看到用于配置检查点、错误和管理的选项。 对于工作区，这些类别会配置源，以便在用户订阅应用列表时进行跟踪。 若要详细了解诊断设置，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。

     >[!IMPORTANT]
     >请记得为需要监视的每个 Azure 资源管理器对象都启用诊断。 在启用了诊断之后，数据将可用于活动。 在首次设置之后，此过程可能需要几个小时。

5. 输入设置配置的名称，然后选择“发送到 Log Analytics”。 你使用的名称不应包含空格，并且应符合 [Azure 命名约定](../azure-resource-manager/management/resource-name-rules.md)。 作为日志的一部分，你可以选择所有想要添加到 Log Analytics 的选项，如检查点、错误、管理等。

6. 选择“保存”。

>[!NOTE]
>Log Analytics 提供将数据流式传输到[事件中心](../event-hubs/event-hubs-about.md)或在存储帐户中存档的选项。 若要详细了解此功能，请参阅[将 Azure 监视数据流式传输到事件中心](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)和[将 Azure 资源日志存档到存储帐户](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。

## <a name="how-to-access-log-analytics"></a>如何访问 Log Analytics

可以在 Azure 门户或 Azure Monito 上访问 Log Analytics 工作区。

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>在 Log Analytics 工作区上访问 Log Analytics

1. 登录到 Azure 门户。

2. 搜索 Log Analytics 工作区。

3. 在“服务”下，选择“Log Analytics 工作区”。

4. 从列表中选择为 Windows 虚拟桌面对象配置的工作区。

5. 在工作区中，选择“日志”。 可以使用“搜索”功能来筛选菜单列表。

### <a name="access-log-analytics-on-azure-monitor"></a>在 Azure Monitor 上访问 Log Analytics

1. 登录到 Azure 门户

2. 搜索并选择“Monitor”。

3. 选择“日志”。 

4. 请按照日志记录页中的说明来设置查询的范围。

5. 你已准备就绪，可以查询诊断了。 所有的诊断表都有“WVD”前缀。

>[!NOTE]
>有关 Azure Monitor 日志中存储的表的更多详细信息，请参阅 [Azure Monitor 数据参考](/azure/azure-monitor/reference/)。 所有与 Windows 虚拟桌面相关的表都会标记为“WVD”。

## <a name="cadence-for-sending-diagnostic-events"></a>发送诊断事件的节奏

在完成之后，诊断事件会发送到 Log Analytics。

Log Analytics 只在连接活动的以下中间状态进行报告：

- 已开始：用户在远程桌面客户端中选择并连接到应用或桌面时。
- 已连接：在用户成功连接到承载应用或桌面的 VM 时。
- 已完成：在用户或服务器与发生活动的会话断开连接时。

## <a name="example-queries"></a>查询示例

通过 Azure Monitor Log Analytics UI 访问示例查询：
1. 转到 Log Analytics 工作区，然后选择“日志”。 该示例查询 UI 会自动显示。
1. 将筛选器更改为“类别”。
1. 选择”Windows 虚拟桌面”来查看可用查询。
1. 选择“运行”以运行所选查询。

请在 [Azure Monitor Log Analytics 中保存的查询](../azure-monitor/logs/example-queries.md)中详细了解该示例查询界面。

下面的查询列表可用于查看某一个用户的连接信息或问题。 可以在 [Log Analytics 查询编辑器](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)中运行这些查询。 对于每个查询，将 `userupn` 替换为要查找的用户的 UPN。


若要查找某一个用户的所有连接：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


若要查找某个用户每天连接的次数：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

若要按用户查找会话持续时间：

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

若要查找某个特定用户的错误：

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

若要了解其他用户是否发生了某个特定错误：

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- 在用户打开完整桌面时，会话中他们的应用使用情况不会在 WVDCheckpoints 表中作为检查点被跟踪。
>- WVDConnections 表中的 ResourcesAlias 列显示用户是连接到完整桌面还是连接到某个已发布的应用。 该列只显示他们在连接期间打开的第一个应用。 用户打开的任何已发布的应用都会在 WVDCheckpoints 中被跟踪。
>- WVDErrors 表会显示管理错误、主机注册问题以及用户订阅应用或桌面的列表时出现的其他问题。
>- WVDErrors 有助于识别管理员任务可以解决的问题。 对于这些类型的问题，ServiceError 上的值始终是“false”。 如果 ServiceError =“true”，则需要将该问题升级到 Microsoft。 请确保为升级的错误提供 CorrelationID。

## <a name="next-steps"></a>后续步骤

若要查看诊断功能可以识别的常见错误场景，请参阅[识别和诊断问题](diagnostics-role-service.md#common-error-scenarios)。