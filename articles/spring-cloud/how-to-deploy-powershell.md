---
title: 如何使用 Azure PowerShell 部署 Azure Spring Cloud
description: 如何使用 Azure PowerShell 部署 Azure Spring Cloud
author: karlerickson
ms.author: karler
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0badb1954cfbd78bc347bf949a6f7ad29bc0177
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014817"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>使用 Azure PowerShell 部署 Azure Spring Cloud

本文介绍如何使用 [Az.SpringCloud](/powershell/module/Az.SpringCloud) PowerShell 模块创建 Azure Spring Cloud 实例。

## <a name="requirements"></a>要求

* 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

   > [!IMPORTANT]
   > 尽管 Az.SpringCloud PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

   ```azurepowershell-interactive
   Install-Module -Name Az.SpringCloud
   ```

* 如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在指定位置创建具有指定名称的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>预配 Azure Spring Cloud 的新实例

若要创建 Azure Spring Cloud 的新实例，请使用 [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) cmdlet。 以下示例在先前创建的资源组中创建具有指定名称的 Azure Spring Cloud 服务。

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>创建新的 Azure Spring Cloud 应用

若要创建新应用，请使用 [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) cmdlet。 以下示例创建名为 `gateway` 的 Azure Spring Cloud 应用。

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>创建新的 Azure Spring Cloud 部署

若要创建新部署，请使用 [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) cmdlet。 以下示例为 `gateway` 应用创建名为 `default` 的 Azure Spring Cloud 部署。

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>获取 Azure Spring Cloud 服务

若要获取 Azure Spring Cloud 服务及其属性，请使用 [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) cmdlet。 以下示例检索有关指定的 Azure Spring Cloud 服务的信息。

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>获取 Azure Spring Cloud 应用

若要获取 Azure Spring Cloud 应用及其属性，请使用 [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) cmdlet。 以下示例检索有关 `gateway` Spring Cloud 应用的信息。

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>获取 Azure Spring Cloud 部署

若要获取 Azure Spring Cloud 部署及其属性，请使用 [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) cmdlet。 以下示例检索有关 `default` Spring Cloud 部署的信息。

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用本文中创建的资源，可以运行以下示例将其删除。

### <a name="delete-an-azure-spring-cloud-deployment"></a>删除 Azure Spring Cloud 部署

若要删除 Azure Spring Cloud 部署，请使用 [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) cmdlet。 以下示例为指定的服务和应用删除名为 `default` 的 Azure Spring Cloud 应用部署。

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>删除 Azure Spring Cloud 应用

若要删除 Spring Cloud 应用，请使用 [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) cmdlet。 以下示例删除指定服务和资源组中的 `gateway` 应用。

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>删除 Azure Spring Cloud 服务

若要删除 Azure Spring Cloud 服务，请使用 [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) cmdlet。 以下示例删除指定的 Azure Spring Cloud 服务。

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>删除资源组

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>后续步骤

[Azure Spring Cloud 开发人员资源](./resources.md)。
