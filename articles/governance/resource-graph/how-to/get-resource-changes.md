---
title: 获取资源更改
description: 了解如何查找资源的更改时间，获取已更改属性的列表以及评估差异。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: ff82a5c3d2f58ade8637d2f44308f9eeda11d09a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324582"
---
# <a name="get-resource-changes"></a>获取资源更改

在日常使用、重新配置甚至是重新部署的过程中，资源都会发生更改。
更改可能是个人或者自动化过程做出的。 大部分更改是依设计做出的，但有时并非如此。 Azure Resource Graph 提供过去 14 天的更改历史记录，可让你：

- 查找在 Azure 资源管理器属性中检测到更改的时间
- 对于每个资源更改，请参阅属性更改详细信息
- 查看检测到的更改之前和之后的资源的完整比较

对于以下示例场景，更改检测和详细信息很有用：

- 在事件管理期间了解可能相关的更改。 查询特定时段内的更改事件，并评估更改详细信息。
- 使配置管理数据库（称为 CMDB）保持最新。 无需按计划的频率刷新所有资源及其完整的属性集，只获取更改的内容。
- 了解当某个资源更改了符合性状态时可能已更改的其他属性。 评估这些附加属性可以洞察可能需要通过 Azure Policy 定义进行管理的其他属性。

本文介绍如何通过 Resource Graph 的 SDK 收集此信息。 若要在 Azure 门户中查看此信息，请参阅 Azure Policy 的[更改历史记录](../../policy/how-to/determine-non-compliance.md#change-history)或 Azure 活动日志[更改历史记录](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)。 有关从基础结构层对应用程序所做的更改的详细信息，请参阅 Azure Monitor 中的[使用应用程序更改分析（预览版）](../../../azure-monitor/app/change-analysis.md)。

> [!NOTE]
> Resource Graph 中的更改详细信息适用于资源管理器属性。 若要跟踪虚拟机内的更改，请参阅 Azure 自动化的[更改跟踪](../../../automation/change-tracking/overview.md)或 Azure Policy 的 [VM 来宾配置](../../policy/concepts/guest-configuration.md)。

> [!IMPORTANT]
> Azure Resource Graph 中的更改历史记录目前以公共预览版提供。

## <a name="find-detected-change-events-and-view-change-details"></a>查找检测到的更改事件并查看更改详细信息

查看资源发生的更改的第一步是查找某个时段内与该资源相关的更改事件。 每个更改事件还包含有关资源的哪些信息发生更改的详细信息。 此步骤通过 resourceChanges REST 终结点完成。

resourceChanges 终结点接受请求正文中的以下参数：

- resourceId \[必需\]：要在其上查找更改的 Azure 资源。
- interval \[必需\]：具有使用“祖鲁语时区 (Z)”检查更改事件的 start 和 end 日期的属性。
- fetchPropertyChanges（可选）：一个布尔属性，设置响应对象是否包括属性更改。

示例请求正文：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

对于上述请求正文，resourceChanges 的 REST API URI 为：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

响应如以下示例所示：

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

resourceId 的每个检测到的更改事件都具有以下属性：

- changeId - 此值对于该资源是唯一的。 changeId 字符串有时可能包含其他属性，但只能保证它是唯一的。
- beforeSnapshot - 包含检测到更改之前创建的资源快照的 snapshotId 和 timestamp。
- afterSnapshot - 包含检测到更改之后创建的资源快照的 snapshotId 和 timestamp。
- changeType - 描述已针对 beforeSnapshot 和 afterSnapshot 之间的完整更改记录检测到的更改的类型。 值为：Create、Update 和 Delete。   仅当 changeType 为 Update 时，才包括 propertyChanges 属性数组。

  > [!IMPORTANT]
  > Create 仅可用于以前存在并且在过去 14 天内删除的资源。

- propertyChanges - 此属性数组提供了 beforeSnapshot 和 afterSnapshot 之间已更新的所有资源属性的详细信息：
  - propertyName - 已更改的资源属性的名称。
  - changeCategory - 描述更改者类别。 值为：System 和 User。
  - changeType - 描述已为单个资源属性检测到的更改的类型。
    值为：Insert、Update、Remove。  
  - beforeValue - beforeSnapshot 中资源属性的值。 当 changeType 为 Insert 时，不会显示。
  - afterValue - afterSnapshot 中资源属性的值。 当 changeType 为 Remove 时，不会显示。

## <a name="compare-resource-changes"></a>比较资源更改

有了来自 resourceChanges 终结点的 changeId 后，就可以使用 resourceChangeDetails REST 终结点获取资源更改之前和之后的快照。  

resourceChangeDetails 终结点要求在请求正文中使用两个参数：

- resourceId：要比较其更改的 Azure 资源。
- changeId：从 resourceChanges 收集的 resourceId 的唯一更改事件。 

示例请求正文：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

对于上述请求正文，resourceChangeDetails 的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

响应如以下示例所示：

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

beforeSnapshot 和 afterSnapshot 分别提供快照创建时间以及当时的属性。 更改是在这些快照之间的某个时间点发生的。 在以上示例中我们可以看到，更改的属性是 supportsHttpsTrafficOnly。

若要对结果进行比较，请使用 resourceChanges 中的 changes 属性，或评估 resourceChangeDetails 中每个快照的 content 部分，以确定差异。    如果对快照进行比较，timestamp 始终会显示为差异，不过这符合预期。

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用的语言。
- 请参阅[高级查询](../samples/advanced.md)中的高级用法。
- 详细了解如何[浏览资源](../concepts/explore-resources.md)。
- 若要了解如何频繁地执行查询，请参阅[针对受限制请求的指南](../concepts/guidance-for-throttled-requests.md)。
