---
title: 使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions
description: 使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell - devx-track-azurepowershell - mode-api
ms.openlocfilehash: 62f1cdcc51779351d743c5e3be5b8405a0bee2d1
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567643"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 部署 Azure Monitor for SAP Solutions

本文介绍了如何使用 [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell 模块创建 Azure Monitor for SAP Solutions 资源。

> [!CAUTION]
> Azure Monitor for SAP Solutions 当前为公共预览版。 此预览版本未提供服务级别协议。 对于生产型工作负荷，不建议使用该预览版。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>要求

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果你选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块。 还需要使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到你的 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 或者，可以选择使用 Cloud Shell。 有关 Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 概述](../../../cloud-shell/overview.md)。

> [!IMPORTANT]
> 尽管 **Az.HanaOnAzure** PowerShell 模块为预览版，但你必须使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../../../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在指定位置创建具有指定名称的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP 监视器

若要创建 SAP 监视器，请使用 [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) cmdlet。 以下示例为指定的订阅、资源组和资源名称创建 SAP 监视器。

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

若要检索 SAP 监视器的属性，请使用 [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) cmdlet。 以下示例获取指定订阅、资源组和资源名称的 SAP 监视器的属性。

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>提供程序实例

若要创建提供程序实例，请使用 [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) cmdlet。 以下示例为指定的订阅、资源组和资源名称创建提供程序实例。

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

若要检索提供程序实例的属性，请使用 [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) cmdlet。 以下示例获取以下各项的属性： 
- 指定的订阅的提供程序实例
- 资源组
- SapMonitor 名称
- 资源名称

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用本文中创建的资源，可以运行以下示例将其删除。

### <a name="delete-the-provider-instance"></a>删除提供程序实例

若要删除提供程序实例，请使用 [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) cmdlet。 以下示例为指定的订阅、资源组、SapMonitor 名称和资源名称删除提供程序实例。

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>删除 SAP 监视器

若要删除 SAP 监视器，请使用 [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) cmdlet。 以下示例删除指定订阅、资源组和监视器名称的 SAP 监视器。

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>删除资源组

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

详细了解 Azure Monitor for SAP Solutions。

> [!div class="nextstepaction"]
> [Azure Monitor for SAP Solutions](azure-monitor-overview.md)
