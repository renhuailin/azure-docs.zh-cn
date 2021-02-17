---
title: 将使用情况数据、指标和日志上传到 Azure Monitor
description: 将资源清单、使用情况数据、指标和日志上传到 Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: ac9c8efbe29bf1420a94d486b650758cc22bec2f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575758"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>将使用情况数据、指标和日志上传到 Azure Monitor

你可以定期为计费目的、监视指标和日志导出使用情况信息，然后将其上传到 Azure。 这三种类型的数据的导出和上传还将在 Azure 中创建和更新数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组资源。

> [!NOTE] 
> 在预览期间，使用启用了 Azure Arc 的数据服务不会产生费用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

上载使用情况数据、指标或日志之前，需要执行以下操作：

* 安装工具 
* [注册 `Microsoft.AzureData` 资源提供程序](#register-the-resource-provider) 
* [创建服务主体](#create-service-principal)

## <a name="install-tools"></a>安装工具

必需的工具包括： 
* Azure CLI (az)  
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

请参阅 [安装工具](./install-client-tools.md)。

## <a name="register-the-resource-provider"></a>注册资源提供程序

在将指标或用户数据上传到 Azure 之前，需要确保 Azure 订阅已 `Microsoft.AzureArcData` 注册了资源提供程序。

若要验证资源提供程序，请运行以下命令：

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

如果你的订阅中当前未注册资源提供程序，则可以注册它。 若要注册，请运行以下命令。  完成此命令可能需要一到两分钟的时间。

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>创建服务主体

服务主体用于上载使用情况和指标数据。

按照以下命令创建指标上载服务主体：

> [!NOTE]
> 若要创建服务主体，需要 [在 Azure 中具有特定权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

若要创建服务主体，请更新以下示例。 `<ServicePrincipalName>`将替换为服务主体的名称，并运行以下命令：

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

如果前面创建了服务主体，只需获取当前凭据，请运行以下命令来重置凭据。

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

例如，若要创建名为的服务主体 `azure-arc-metrics` ，请运行以下命令

```
az ad sp create-for-rbac --name azure-arc-metrics
```

示例输出：

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

将 `appId` 、 `password` 和值保存 `tenant` 在环境变量中供以后使用。 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

在创建服务主体后，将服务主体分配给相应的角色。 

## <a name="assign-roles-to-the-service-principal"></a>向服务主体分配角色

运行此命令，将服务主体分配到 `Monitoring Metrics Publisher` 数据库实例资源所在的订阅上的角色：

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> 在 Windows 环境中运行时，你需要为角色名称使用双引号。

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

示例输出：

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

将服务主体分配到适当的角色后，可以继续上传度量值或用户数据。 

## <a name="upload-logs-metrics-or-user-data"></a>上传日志、指标或用户数据

用于上传日志、指标或用户数据的具体步骤因你要上载的信息类型而异。 

[将日志上传到 Azure Monitor](upload-logs.md)

[将指标上载到 Azure Monitor](upload-metrics.md)

[将使用情况数据上传到 Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>有关导出和上载使用情况的一般指南，指标

在启用了 Azure Arc 的数据服务上创建、读取、更新和删除 (CRUD) 操作，以便进行计费和监视。 存在监视这些 CRUD 操作并相应计算消耗的后台服务。 实际使用情况或消耗计算按计划进行，并在后台完成。 

在预览期间，此过程在夜间发生。 一般原则是每天仅上载一次使用。 当在同一个24小时内将使用情况信息导出并上传多次时，只 Azure 门户中的资源清单进行更新，而不会更新资源使用情况。

对于上传指标，Azure monitor 只接受过去30分钟的数据 ([了解更多](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)) 。 上传指标的指导是在创建导出文件后立即上载指标，以便查看 Azure 门户中的整个数据集。 例如，如果在 2:00 PM 导出指标，并在 2:50 PM 运行上传命令。 由于 Azure Monitor 仅接受过去30分钟的数据，因此你可能在门户中看不到任何数据。 

## <a name="next-steps"></a>后续步骤

[了解服务主体](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[将计费数据上传到 Azure 并在 Azure 门户中查看](view-billing-data-in-azure.md)

[查看 Azure 门户中的 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)