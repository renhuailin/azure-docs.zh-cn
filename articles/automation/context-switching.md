---
title: Azure 自动化中的上下文切换
description: 本文介绍上下文切换，以及如何避免 runbook 问题。
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: 121e302708fecd20934f6ba57bd08590e45a7429
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233193"
---
# <a name="context-switching-in-azure-automation"></a>Azure 自动化中的上下文切换

上下文切换是指一个进程中的上下文更改另一个进程中的上下文。 Azure 上下文是一组定义 Azure PowerShell cmdlet 目标的信息。 上下文包括下列属性：

|属性 | 说明 |
|---|---|
|名称 | 上下文名称。|
|帐户 | 对 Azure 通信进行身份验证时所用的用户名或服务主体。|
|环境 | 表示 Azure 全球或国家/地区 Azure 云之一，例如 Azure 政府。 还可以指定混合云平台，如 Azure Stack。|
|订阅 | 表示包含要管理的资源的 Azure 订阅。|
|租户 | 表示单个组织的专用且受信任的 Azure Active Directory 实例。|
|凭据 | Azure 用来验证你的身份并确保你有权访问 Azure 中的资源的信息。|

当可以访问多个订阅的帐户登录时，可以将这些订阅中的任何一个添加到用户的上下文中。 若要保证正确的订阅，必须在连接时声明。 例如，使用 `Add-AzAccount -Credential $Cred -subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749'`。 但是，当管理一个订阅的 runbook 与管理同一自动化帐户的另一个订阅中的资源的其他 runbook 在同一沙盒进程中运行时，可能会出现问题。 一个 runbook 对上下文所做的更改可能会影响使用默认上下文的其他 runbook。 当上下文包含信息（例如要使用的凭据和目标订阅）时，cmdlet 可能会将错误的订阅设为目标，从而导致 `not found` 或权限错误。 此问题称为上下文切换。

## <a name="manage-azure-contexts"></a>管理 Azure 上下文

若要避免 runbook 针对错误订阅中的资源运行，请查看以下建议：

1. 通过在每个 runbook 的开头使用以下命令，禁止在自动化 runbook 中保存沙盒上下文：`Disable-AzContextAutosave -Scope Process`。
1. Azure PowerShell cmdlet 支持 `-DefaultProfile` 参数。 此参数已添加到所有 Az 和 Azure 资源管理器 (AzureRM) cmdlet 以支持在同一进程中运行多个脚本，从而允许你为每个 cmdlet 指定要使用的上下文。 在创建和每次更改上下文对象时将其保存在 runbook 中。 然后在每次使用 Az 或 AzureRM cmdlet 进行的调用中引用它。 例如，`$AzureContext = Set-AzContext -SubscriptionId $subID`。
1. 将上下文对象传递到 PowerShell cmdlet，例如 `Get-AzVM -ResourceGroupName "myGroup" -DefaultProfile $AzureContext`。

下面是一个 PowerShell runbook 代码片段，它使用系统分配的托管标识，这些标识遵循避免上下文切换的建议。

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Pass context object - even though the context had just been set
# This is the step that guarantees the context will not be switched.
Get-AzVM -ResourceGroupName "resourceGroupName" -DefaultProfile $AzureContext | Select Name
```

## <a name="possible-symptoms"></a>可能出现的症状

如果不遵循这些建议，可能不会遇到问题，但机会确实存在。 这种情况的根本问题是时机；这取决于每个 runbook 在另一个 runbook 切换其上下文时正在做什么。 下面是一些可能的错误消息。 但是，这些错误消息可能是由非上下文切换条件导致的。

`The subscription named <subscription name> cannot be found.`

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

```error
Get-AzureRmResource : Resource group "SomeResourceGroupName" could not be found.
... resources = Get-AzResource -ResourceGroupName $group.ResourceGro ...
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Get-AzResource], CloudException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.ResourceManager.Cmdlets.Implementation.GetAzureResourceCmdlet
```

## <a name="next-steps"></a>后续步骤

- [Azure 自动化帐户身份验证概述](automation-security-overview.md)
- [在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)
- [管理 Azure 自动化中的模块](./shared-resources/modules.md)

