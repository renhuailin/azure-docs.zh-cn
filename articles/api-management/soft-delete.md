---
title: Azure API 管理软删除（预览版）|Microsoft Docs
description: 通过软删除，可以恢复已删除的 API 管理实例。
ms.service: api-management
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 11/27/2020
ms.openlocfilehash: 0cf99dfe2fb697b0a28db1fbacecaa2e2021a9d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622011"
---
# <a name="api-management-soft-delete-preview"></a>API 管理软删除（预览版）

利用 API 管理软删除（预览），你可以恢复和还原最近删除的 API 管理 (APIM) 实例。

> [!IMPORTANT]
> 只有使用 `2020-06-01-preview` 和更高的 API 版本删除的 API 管理实例才会被软删除并且可以使用本文所述步骤来恢复。 使用以前的 API 版本删除的 APIM 实例将会继续被硬删除。 Azure PowerShell 和 Azure CLI 目前不使用 `2020-06-01-preview` 版本，并且也会导致硬删除行为。

## <a name="supporting-interfaces"></a>支持接口

可以通过 [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore) 使用软删除功能。

> [!TIP]
> 请参考 [Azure REST API 参考](/rest/api/azure/)，了解有关调用 Azure REST API 的提示和工具。

| 操作 | 说明 | API 管理命名空间 | 最低 API 版本 |
|--|--|--|--|
| [创建或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | 创建或更新 API 管理服务。  | API 管理服务 | 任意 |
| `restore` 属性设置为 true 的[创建或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | 取消删除 API 管理服务（如果以前已软删除）。 如果 `restore` 已指定并设为 `true`，则会忽略所有其他属性。  | API 管理服务 |  2020-06-01-preview |
| [删除](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | 删除现有的 API 管理服务。 | API 管理服务 | 2020-06-01-preview|
| [按名称获取](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | 按名称获取已软删除的 API 管理服务。 | 已删除的服务 | 2020-06-01-preview |
| [按订阅列出](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | 列出对于给定的订阅可以取消删除的所有已软删除的服务。 | 已删除的服务 | 2020-06-01-preview
| [清除](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | 清除 API 管理服务（将其删除，没有取消删除的选项）。 | 已删除的服务 | 2020-06-01-preview

## <a name="soft-delete-behavior"></a>软删除行为

可以使用任何 API 版本来创建 API 管理实例，但是，需要使用 `2020-06-01-preview` 或更高版本来软删除 APIM 实例（并且可以选择恢复该实例）。

在删除 API 管理实例时，该服务将处于已删除状态，任何 APIM 操作都无法访问该服务。 在此状态下，该 APIM 实例只能被列出、恢复或清除（永久删除）。

同时，Azure 将会计划在预定（48 小时）保留间隔之后执行删除对应于 APIM 实例的基础数据的操作。 在保留间隔期间，还会保留与该实例对应的 DNS 记录。 在保留期结束之前，不能重复使用已软删除的 API 管理实例的名称。

如果在 48 小时内未恢复 APIM 实例，则会将其硬删除（无法恢复）。 还可以选择[清除](#purge-a-soft-deleted-apim-instance)（永久删除）APIM 实例，放弃软删除保留期。

## <a name="list-deleted-apim-instances"></a>列出已删除的 APIM 实例

可以使用已删除的服务[按名称获取](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname)或[按订阅列出](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription)操作来验证已软删除的 APIM 实例是否可进行还原（取消删除）。

### <a name="get-a-soft-deleted-instance-by-name"></a>按名称获取已软删除的实例

使用 API 管理[按名称获取](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname)操作，将 `{subscriptionId}`、`{location}` 和 `{serviceName}` 替换为你的 Azure 订阅、资源位置和 API 管理实例名称：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

如果可以取消删除，Azure 将会返回该 APIM 实例的记录，显示其 `deletionDate` 和 `scheduledPurgeDate`，例如：

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>列出某个给定订阅的所有软删除实例

使用 API 管理[按订阅列出](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription)操作，将 `{subscriptionId}` 替换为你的订阅 ID：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

此操作将会返回列表，列出给定订阅下所有可以取消删除的已软删除的服务，显示每个服务的 `deletionDate` 和 `scheduledPurgeDate`。

## <a name="recover-a-deleted-apim-instance"></a>恢复已删除的 APIM 实例

使用 API 管理 [创建或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate)操作，将 `{subscriptionId}`、`{resourceGroup}` 和 `{apimServiceName}` 替换为你的 Azure 订阅、资源组名称和 API 管理名称：

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . 并在请求正文中将 `restore` 属性设置为 `true`。 （如果指定了此标志并将其设为 *true*，那么，所有其他属性都会被忽略。）例如：

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>清除已软删除的 APIM 实例

使用 API 管理[清除](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge)操作，将 `{subscriptionId}`、`{location}` 和 `{serviceName}` 替换为你的 Azure 订阅、资源位置和 API 管理名称：

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

这样将会从 Azure 永久删除你的 API 管理实例。

## <a name="next-steps"></a>后续步骤

了解长期 API 管理备份和恢复选项：

- [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](api-management-howto-disaster-recovery-backup-restore.md)