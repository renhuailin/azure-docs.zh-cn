---
title: 快速入门：使用 Azure 资源管理器模板（ARM 模板）配置网络安全组流日志
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）和 Azure PowerShell 以编程方式启用网络安全组 (NSG) 流日志。
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom: devx-track-azurepowershell - subject-armqs - mode-arm
ms.openlocfilehash: 953e61febcd965786f2a53f2769c1d52c85f83a3
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112452667"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>快速入门：使用 ARM 模板配置网络安全组流日志

本快速入门介绍如何使用 [Azure 资源管理器](../azure-resource-manager/management/overview.md)模板（ARM 模板）和 Azure PowerShell 启用[网络安全组 (NSG) 流日志](network-watcher-nsg-flow-logging-overview.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

首先，我们将概述 NSG 流日志对象的属性。 我们将提供示例模板。 然后，我们将使用本地 Azure PowerShell 实例部署模板。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 模板将在 Azure 门户中打开。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fnetworkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/networkwatcher-flowlogs-create/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/networkwatcher-flowLogs-create/azuredeploy.json":::

该模板中定义了以下资源：

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG 流日志对象

以下代码显示了 NSG 流日志对象及其参数。 若要创建 `Microsoft.Network/networkWatchers/flowLogs` 资源，请将此代码添加到模板的资源部分：

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

有关 NSG 流日志对象属性的完整概述，请参阅 [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs)。

## <a name="create-your-template"></a>创建模板

如果你是第一次使用 ARM 模板，请参阅以下文章，了解有关 ARM 模板的详细信息：

- [使用 ARM 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [创建并部署第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

下面的示例是一个完整的模板。 它也是最简单的模板版本。 该示例包含设置 NSG 流日志所需传递的最少参数。 有关更多示例，请参阅概述文章[根据 Azure 资源管理器模板配置 NSG 流日志](network-watcher-nsg-flow-logging-azure-resource-manager.md)。

### <a name="example"></a>示例

以下模板将为 NSG 启用流日志，然后将日志存储在特定的存储帐户中：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - 资源名称的格式为 ParentResource_ChildResource。 在我们的示例中，父资源为区域 Azure 网络观察程序实例：
>    - Format：NetworkWatcher_RegionName
>    - **示例**：NetworkWatcher_centraluseuap
> - `targetResourceId` 是目标 NSG 的资源 ID。
> - `storageId` 是目标存储帐户的资源 ID。

## <a name="deploy-the-template"></a>部署模板

本教程假定你已有一个资源组和一个可以启用流日志记录的 NSG。

你可以在本地将本文所示的任意示例模板另存为 azuredeploy.json。 更新属性值，使其指向订阅中的有效资源。

若要部署模板，请在 Azure PowerShell 中运行以下命令：

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 这些命令会将资源部署到示例 NetworkWatcherRG 资源组，而不是包含 NSG 的资源组。

## <a name="validate-the-deployment"></a>验证部署

可通过两个选项来查看部署是否成功：

- PowerShell 控制台将 `ProvisioningState` 显示为 `Succeeded`。
- 转到 [NSG 流日志门户页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)确认所做的更改。

如果部署出现问题，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](../azure-resource-manager/templates/common-deployment-errors.md)。

## <a name="clean-up-resources"></a>清理资源

可以使用完整部署模式删除 Azure 资源。 若要删除流日志资源，请在完整模式下指定部署，而不包含要删除的资源。 详细了解[完整部署模式](../azure-resource-manager/templates/deployment-modes.md#complete-mode)。

还可以在 Azure 门户中禁用 NSG 流日志：

1. 登录到 Azure 门户。
1. 选择“所有服务”。 在“筛选器”框中，输入“网络观察程序” 。 在搜索结果中，选择“网络观察程序”。
1. 在“日志”下，选择“NSG 流日志” 。
1. 在 NSG 列表中，选择要为其禁用流日志的 NSG。
1. 在“流日志设置”下选择“关闭” 。
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 ARM 模板来启用 NSG 流日志。 接下来，了解如何使用以下选项之一可视化 NSG 流数据：

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [开源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure 流量分析](traffic-analytics.md)
