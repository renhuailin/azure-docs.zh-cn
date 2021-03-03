---
title: Azure API 管理软删除 (预览版) |Microsoft Docs
description: 通过软删除，可以恢复已删除的 API 管理实例。
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652379"
---
# <a name="api-management-soft-delete-preview"></a>API 管理软删除 (预览) 

利用 API 管理软删除 (预览) ，你可以恢复和还原最近删除的 API 管理 (APIM) 实例。

> [!IMPORTANT]
> `2020-06-01-preview`使用和更高版本的 api 将仅删除使用和更高版本的 Api 管理实例，并使用本文中所述的步骤进行恢复。 使用以前的 API 版本删除的 APIM 实例将继续硬删除。 Azure PowerShell 和 Azure CLI 目前不使用该 `2020-06-01-preview` 版本，也会导致硬删除行为。

## <a name="supporting-interfaces"></a>支持接口

软删除功能通过 [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore)提供。

> [!TIP]
> 请参阅 [azure REST API 参考](/rest/api/azure/) ，了解调用 Azure REST api 的提示和工具。

| Operation | 说明 | API 管理命名空间 | 最小 API 版本 |
|--|--|--|--|
| [创建或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | 创建或更新 API 管理服务。  | API 管理服务 | 任意 |
| [创建或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) `restore` 属性设置为 **true** 的 | Undeletes API 管理服务（如果以前已软删除）。 如果 `restore` 已指定，并且设置为 `true` 所有其他属性，则将被忽略。  | API 管理服务 |  2020-06-01-预览 |
| [删除](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | 删除现有的 API 管理服务。 | API 管理服务 | 2020-06-01-预览|
| [按名称获取](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | 按名称获取软删除的 Api 管理服务。 | 删除的服务 | 2020-06-01-预览 |
| [按订阅列出](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | 列出给定订阅可删除的所有软删除的服务。 | 删除的服务 | 2020-06-01-预览
| [清空](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | 清除 API 管理服务 (会将其删除，并且不会删除) 的选项。 | 删除的服务 | 2020-06-01-预览

## <a name="soft-delete-behavior"></a>软删除行为

你可以使用任何 API 版本来创建 API 管理实例，但是，你需要使用 `2020-06-01-preview` 或更高版本来软删除你的 APIM 实例 (并且可以选择将其恢复) 。

删除 API 管理实例时，该服务将处于已删除状态，从而使任何 APIM 操作都无法访问该服务。 在此状态下，APIM 实例只能列出、恢复或清除 (永久删除的) 中。

同时，Azure 将计划在预先确定的 (48 小时) 保留间隔后，删除与 APIM 实例对应的基础数据。 与实例相对应的 DNS 记录也会在保留间隔期间保留。 在保留期结束之前，不能重复使用已软删除的 API 管理实例的名称。

如果在48小时内未恢复 APIM 实例，则 (无法恢复的) 会将其硬删除。 你还可以选择 [清除](#purge-a-soft-deleted-apim-instance) (永久删除) APIM 实例，放弃软删除保留期。

## <a name="list-deleted-apim-instances"></a>列出已删除的 APIM 实例

你可以使用已删除的服务通过名称或[列表按订阅](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription)操作[获取](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname)，来验证软删除的 APIM 实例是否可用于还原 (取消删除) 。

### <a name="get-a-soft-deleted-instance-by-name"></a>按名称获取软删除实例

使用 API 管理 " [按名称获取](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) " 操作，将 `{subscriptionId}` 、 `{location}` 和替换 `{serviceName}` 为 Azure 订阅、资源位置和 API 管理实例名称：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

如果可撤消删除，Azure 将返回 APIM 实例的记录，其中显示了 `deletionDate` 和 `scheduledPurgeDate` ，例如：

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

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>列出给定订阅的所有软删除实例

[按订阅](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription)操作使用 API 管理列表，将替换 `{subscriptionId}` 为你的订阅 ID：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

这会返回一个列表，其中列出了给定订阅下可删除的所有软删除服务，其中显示了 `deletionDate` 每个服务的和 `scheduledPurgeDate` 。

## <a name="recover-a-deleted-apim-instance"></a>恢复已删除的 APIM 实例

使用 API 管理 [创建或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) 操作，将 `{subscriptionId}` 、和替换 `{resourceGroup}` `{apimServiceName}` 为你的 Azure 订阅、资源组名称和 API 管理名称：

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . 并 `restore` `true` 在请求正文中将属性设置为。  (如果指定此标志并将其设置为 *true*，则将忽略所有其他属性。 ) 例如：

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

## <a name="purge-a-soft-deleted-apim-instance"></a>清除软删除的 APIM 实例

使用 API 管理 [清除](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) 操作，将 `{subscriptionId}` 、和替换 `{location}` `{serviceName}` 为你的 Azure 订阅、资源位置和 API 管理名称：

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

这将从 Azure 中永久删除你的 API 管理实例。

## <a name="next-steps"></a>后续步骤

了解长期 API 管理备份和恢复选项：

- [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](api-management-howto-disaster-recovery-backup-restore.md)