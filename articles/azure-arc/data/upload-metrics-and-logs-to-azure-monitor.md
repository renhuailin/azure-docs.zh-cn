---
title: 将使用情况数据、指标和日志上传到 Azure
description: 将资源清单、使用情况数据、指标和日志上传到 Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 6f314b8918b415c0449722d1229c6de47af1cac5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741442"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure"></a>将使用情况数据、指标和日志上传到 Azure

用户可以定期导出使用情况信息进行计费，监视指标和日志，然后将其上传到 Azure。 导出并上传这三种类型的数据还将在 Azure 中创建并更新数据控制器、SQL 托管实例和超大规模 PostgreSQL 服务器组资源。

在上传使用情况数据、指标或日志之前，需要执行以下操作：

* 安装工具 
* [注册 `Microsoft.AzureArcData` 资源提供程序](#register-the-resource-provider) 
* [创建服务主体](#create-service-principal)

## <a name="install-tools"></a>安装工具

必需的工具包括： 
* Azure CLI (az) 
* `arcdata` 扩展 

请参阅[安装工具](./install-client-tools.md)。

## <a name="register-the-resource-provider"></a>注册资源提供程序

在将指标或用户数据上传到 Azure 之前，需要确保 Azure 订阅已经注册了 `Microsoft.AzureArcData` 资源提供程序。

如果要验证资源提供程序，请运行以下命令：

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

如果该资源提供程序尚未在订阅中注册，可将其注册。 如果要进行注册，请运行以下命令。  完成此命令可能需要一到两分钟的时间。

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>创建服务主体

服务主体用于上传使用情况和指标数据。

请按照以下命令来创建指标上传服务主体：

> [!NOTE]
> 创建服务主体需要 [Azure 中的特定权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

如果要创建服务主体，请更新以下示例。 请将 `<ServicePrincipalName>`、`SubscriptionId` 和 `resourcegroup` 替换为你的值，并运行以下命令：

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/<SubscriptionId>/resourceGroups/<resourcegroup>
```

如果你在前面创建了服务主体，并且只是需要获取当前凭据，那么，请运行以下命令来重置该凭据。

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

例如，如果要创建名为 `azure-arc-metrics` 的服务主体，请运行以下命令

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

示例输出：

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

将 `appId`、`password` 和 `tenant` 值保存在环境变量中，供以后使用。 

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

在创建了服务主体后，请将该服务主体分配到相应的角色。 

## <a name="assign-roles-to-the-service-principal"></a>将角色分配到服务主体

请运行此命令，以将该服务主体分配到数据库实例资源所在的订阅上的 `Monitoring Metrics Publisher` 角色：

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> 在从 Windows 环境中运行时，角色名称需要使用双引号。

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
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

## <a name="verify-service-principal-role"></a>验证服务主体角色

```azurecli
az role assignment list -o table
```

在将服务主体分配到相应的角色后，可以继续上传指标或用户数据。 



## <a name="upload-logs-metrics-or-usage-data"></a>上传日志、指标或使用情况数据

用于上传日志、指标或使用情况数据的具体步骤因要上传的信息类型而异。 

[将日志上传到 Azure Monitor](upload-logs.md)

[将指标上传到 Azure Monitor](upload-metrics.md)

[将使用情况数据上传到 Azure](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-and-metrics"></a>有关导出和上传使用情况和指标的一般原则

针对已启用 Azure Arc 的数据服务的创建、读取、更新和删除 (CRUD) 操作会被记录到日志，以用于计费和监视。 有后台服务会监视这些 CRUD 操作并相应计算消耗。 实际的使用情况或消耗的计算会按计划进行，并且在后台完成。 

每日仅上传一次使用情况数据。 如果在同一个 24 小时内多次导出并上传使用情况信息，则只会更新 Azure 门户中的资源清单，而不会更新资源使用情况。

对于上传指标，Azure Monitor 只接受最近 30 分钟的数据（[了解详细信息](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)）。 上传指标的原则是要在创建导出文件后立即上传指标，这样就可以在 Azure 门户中看到整个数据集。 例如，如果在下午 2:00 导出指标，然后在下午 2:50 运行上传命令。 由于 Azure Monitor 只接受最近 30 分钟的数据，因此在门户中可能看不到任何数据。 

## <a name="next-steps"></a>后续步骤

[了解服务主体](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[将计费数据上传到 Azure 并在 Azure 门户中查看该数据](view-billing-data-in-azure.md)

[在 Azure 门户中查看 Azure Arc 数据控制器资源](view-data-controller-in-azure-portal.md)
