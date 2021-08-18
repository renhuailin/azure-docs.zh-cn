---
title: Azure 虚拟桌面（经典）诊断日志分析 - Azure
description: 如何将日志分析与 Azure 虚拟桌面（经典）诊断功能配合使用。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0be29603225361b2f275f081c1ad52c5c6c394db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736167"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-azure-virtual-desktop-classic"></a>在 Azure 虚拟桌面（经典）中使用 Log Analytics 诊断功能

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../diagnostics-log-analytics.md)。

Azure 虚拟桌面提供了一项诊断功能，使管理员能够通过单个界面识别问题。 每当被分配了 Azure 虚拟桌面角色的用户使用该服务时，此功能就会记录诊断信息。 每个日志都包含有关活动中涉及的 Azure 虚拟桌面角色的信息，还包括在会话过程中出现的任何错误消息，以及租户信息和用户信息。 诊断功能用于为用户和管理操作创建活动日志。 每个活动日志都会归属于三个主要类别：

- 源订阅活动：当用户尝试通过 Microsoft 远程桌面应用程序连接到其源时。
- 连接活动：当用户尝试通过 Microsoft 远程桌面应用程序连接到桌面或 RemoteApp 时。
- 管理活动：当管理员在系统上执行管理操作（如创建主机池、将用户分配到应用组和创建角色分配）时。

由于诊断角色服务本身是 Azure 虚拟桌面的一部分，因此无法访问 Azure 虚拟桌面的连接将不会显示在诊断结果中。 在用户遇到网络连接问题时，可能会出现 Azure 虚拟桌面连接问题。

## <a name="why-you-should-use-log-analytics"></a>为什么要使用 Log Analytics

建议使用 Log Analytics 来分析 Azure 客户端中的诊断数据，而不只是单用户故障排除。 由于你可以将 VM 性能计数器拉取到 Log Analytics，因此你有一个工具可以用来收集部署信息。

## <a name="before-you-get-started"></a>准备工作

你需要[创建工作区](../../azure-monitor/logs/quick-create-workspace.md)，然后才能将 Log Analytics 与诊断功能配合使用。

在创建了工作区之后，请按照[将 Windows 计算机连接到 Azure Monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) 中的说明获取以下信息：

- 工作区 ID
- 工作区的主密钥

稍后在设置过程中将会需要这些信息。

## <a name="push-diagnostics-data-to-your-workspace"></a>将诊断数据推送到工作区

可以将诊断数据从 Azure 虚拟桌面租户推送到工作区的 Log Analytics 中。 首次创建租户时，你可以立即设置此功能，方法是将你的工作区链接到你的租户，也可以稍后使用现有租户进行设置。

若要在设置新租户时将租户链接到 Log Analytics 工作区，请运行以下 cmdlet，以通过 TenantCreator 用户帐户登录到 Azure 虚拟桌面：

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

如果要链接现有租户而不是新租户，请改为运行以下 cmdlet：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

需要为要链接到 Log Analytics 的每个租户运行这些 cmdlet。

>[!NOTE]
>如果不想在创建租户时链接 Log Analytics 工作区，请改为运行 `New-RdsTenant` cmdlet。

## <a name="cadence-for-sending-diagnostic-events"></a>发送诊断事件的节奏

在完成之后，诊断事件会发送到 Log Analytics。

## <a name="example-queries"></a>查询示例

以下示例查询显示了诊断功能如何为系统中最常见的活动生成报告：

第一个示例显示了用户使用受支持的远程桌面客户端发起的连接活动：

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

下面的示例查询显示了管理员在租户上进行的管理活动：

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>停止向 Log Analytics 发送数据

若要停止将数据从现有租户发送到 Log Analytics，请运行以下 cmdlet 并设置空字符串：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

需要为要停止从其发送数据的每个租户运行此 cmdlet。

## <a name="next-steps"></a>后续步骤

若要查看诊断功能可以识别的常见错误场景，请参阅[识别和诊断问题](diagnostics-role-service-2019.md#common-error-scenarios)。
