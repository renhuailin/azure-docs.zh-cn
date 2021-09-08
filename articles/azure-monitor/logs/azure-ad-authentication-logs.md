---
title: 适用于日志的 Azure AD 身份验证
description: 了解如何为 Azure Monitor 中的 Log Analytics 启用 Azure Active Directory (Azure AD) 身份验证。
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 526312df5c1bbf5e21a4017177752fb4f476ae5c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868457"
---
# <a name="azure-ad-authentication-for-logs"></a>适用于日志的 Azure AD 身份验证

Azure Monitor 可以[从多个源收集日志中的数据](data-platform-logs.md#data-collection)，包括虚拟机上的代理、Application Insights、Azure 资源的诊断设置和数据收集器 API。

Log Analytics 代理使用工作区密钥作为注册密钥来验证初始访问权限并预配证书，以便将其进一步用于在代理和 Azure Monitor 之间建立安全连接。 若要了解详细信息，请参阅[从代理发送数据](data-security.md#2-send-data-from-agents)。 数据收集器 API 使用相同的工作区密钥来[授权访问](data-collector-api.md#authorization)。

这些选项可能会比较繁琐并且会带来风险，因为很难大规模管理凭据，特别是工作区密钥。 你可以选择退出本地身份验证，并确保仅将使用托管标识和 Azure Active Directory 专门进行身份验证的遥测引入 Azure Monitor。 此功能可增强遥测的安全性和可靠性，用于做出关键操作和业务决策。

使用以下步骤为 Azure Monitor 日志启用 Azure Active Directory 集成，并删除对这些共享机密的依赖。

1. Azure Monitor 代理 (AMA) 无需任何密钥，而是[需要系统托管标识](../agents/azure-monitor-agent-overview.md#security)。 从 Log Analytics 代理[迁移到 Azure Monitor 代理](../agents/azure-monitor-agent-migration.md)。
2. [禁用 Log Analytics 工作区的本地身份验证](#disable-local-authentication-for-log-analytics)。
3. 通过[适用于 Application Insights 的 Azure AD 身份验证（预览版）](../app/azure-ad-authentication.md)确保只有经过身份验证的遥测才能引入到 Application Insights 资源中。

## <a name="disable-local-authentication-for-log-analytics"></a>禁用 Log Analytics 的本地身份验证

删除对 Log Analytics 代理的依赖后，可以选择禁用 Log Analytics 工作区的本地身份验证。 这就可以引入和查询由 Azure AD 专门验证的遥测。

禁用本地身份验证可能会限制某些可用功能，具体如下：

- 现有 Log Analytics 代理将停止运行，仅支持 Azure Monitor 代理 (AMA)。 Azure Monitor 代理缺少一些通过 Log Analytics 代理提供的功能（例如自定义日志收集、IIS 日志收集）。
- 数据收集器 API（预览版）不支持 Azure AD 身份验证，并且不能用于引入数据。

可使用 Azure Policy 或通过 Azure 资源管理器模板、PowerShell 或 CLI 以编程方式禁用本地身份验证。

### <a name="azure-policy"></a>Azure Policy

Azure Policy“DisableLocalAuth”将拒绝用户在未将此属性设置为“true”的情况下创建新的 Log Analytics 工作区。 策略名称为 `Log Analytics Workspaces should block non-Azure Active Directory based ingestion`。 若要将此策略定义应用到订阅，请[创建新的策略分配并分配该策略](../../governance/policy/assign-policy-portal.md)。 

以下是策略模板定义：

```json
{
  "properties": {
    "displayName": "Log Analytics Workspaces should block non-Azure Active Directory based ingestion.",
    "policyType": "BuiltIn",
    "mode": "Indexed",
    "description": "Enforcing log ingestion to require Azure Active Directory authentication prevents unauthenticated logs from an attacker which could lead to incorrect status, false alerts, and incorrect logs stored in the system.",
    "metadata": {
      "version": "1.0.0",
      "category": "Monitoring"
    },
    "parameters": {
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Enable or disable the execution of the policy"
        },
        "allowedValues": [
          "Deny",
          "Audit",
          "Disabled"
        ],
        "defaultValue": "Audit"
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.OperationalInsights/workspaces"
          },
          {
            "field": "Microsoft.OperationalInsights/workspaces/features.disableLocalAuth",
            "notEquals": "true"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
  },
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e15effd4-2278-4c65-a0da-4d6f6d1890e2",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "e15effd4-2278-4c65-a0da-4d6f6d1890e2"
}
```

### <a name="azure-resource-manager"></a>Azure 资源管理器

属性 `DisableLocalAuth` 用于禁用 Log Analytics 工作区上的任意本地身份验证。 设置为 true 时，此属性强制所有访问都必须使用 Azure AD 身份验证。 

下面是可用于禁用本地身份验证的 Azure 资源管理器模板示例：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaces_name": {
            "defaultValue": "workspace-name",
            "type": "string"
        },
        "workspace_location": {
          "defaultValue": "region-name",
          "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaces_name')]",
            "location": "[parameters('workspace_location')]",
            "properties": {
                 "sku": {
                    "name": "PerGB2018"
                },
                "retentionInDays": 30,
                "features": {
                    "disableLocalAuth": false,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
}

```


### <a name="cli"></a>CLI

属性 `DisableLocalAuth` 用于禁用 Log Analytics 工作区上的任意本地身份验证。 设置为 true 时，此属性强制所有访问都必须使用 Azure AD 身份验证。 

下面是可用于禁用本地身份验证的 CLI 命令示例：

```azurecli
    az resource update --ids "/subscriptions/[Your subscription ID]/resourcegroups/[Your resource group]/providers/microsoft.operationalinsights/workspaces/[Your workspace name]--api-version "2021-06-01" --set properties.features.disableLocalAuth=True
```

### <a name="powershell"></a>PowerShell

属性 `DisableLocalAuth` 用于禁用 Log Analytics 工作区上的任意本地身份验证。 设置为 true 时，此属性强制所有访问都必须使用 Azure AD 身份验证。 

下面是可用于禁用本地身份验证的 PowerShell 命令示例：

```powershell
    $workspaceSubscriptionId = "[You subscription ID]"
    $workspaceResourceGroup = "[You resource group]"
    $workspaceName = "[Your workspace name]"
    $disableLocalAuth = $false
    
    # login
    Connect-AzAccount
    
    # select subscription
    Select-AzSubscription -SubscriptionId $workspaceSubscriptionId
    
    # get private link workspace resource
    $workspace = Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ResourceGroupName $workspaceResourceGroup -ResourceName $workspaceName -ApiVersion "2021-06-01"
    
    # set DisableLocalAuth
    $workspace.Properties.Features | Add-Member -MemberType NoteProperty -Name DisableLocalAuth -Value $disableLocalAuth -Force
    $workspace | Set-AzResource -Force
```

## <a name="next-steps"></a>后续步骤
* [适用于 Application Insights 的 Azure AD 身份验证（预览）](../app/azure-ad-authentication.md)