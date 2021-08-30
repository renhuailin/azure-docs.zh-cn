---
title: 将 Azure Kubernetes 服务用作事件网格源（预览）
description: 本文介绍了如何将 Azure Kubernetes 服务用作事件网格事件源。 其中提供了架构，以及教程和操作指南文章的链接。
author: zr-msft
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: 16b82ea59e69457475966fecfb8a0ca8d7dd20ce
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733858"
---
# <a name="azure-kubernetes-service-aks-as-an-event-grid-source-preview"></a>将 Azure Kubernetes 服务 (AKS) 用作事件网格源（预览）

本文提供 AKS 事件的属性和架构。  有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 它还提供了一个快速入门和教程的列表，介绍如何使用 AKS 作为事件源。

[!INCLUDE [preview features callout](../aks/includes/preview/preview-callout.md)]

## <a name="available-event-types"></a>可用事件类型

AKS 发出以下事件类型

|    事件类型                                             |    说明                                                       |
|-----------------------------------------------------------|----------------------------------------------------------------------|
| Microsoft.ContainerService.NewKubernetesVersionAvailable  | 更新可用的 Kubernetes 版本列表时触发。 |

## <a name="properties-common-to-all-events"></a>所有事件通用的属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。
本部分包含一个示例，介绍每个事件的数据外观。 每个事件具有以下顶级数据：

|     属性          |     类型     |     说明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    string    |    事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。                                      |
|    `subject`            |    string    |    事件主题的发布者定义路径。                                                                                              |
|    `eventType`          |    字符串    |    此事件源的已注册事件类型之一。                                                                                  |
|    `eventTime`          |    字符串    |    事件的生成时间，基于提供程序的 UTC 时间。                                                                         |
|    `id`                 |    字符串    |    事件的唯一标识符。                                                                                                            |
|    `data`               |    object    |    Blob 存储事件数据。                                                                                                                    |
|    `dataVersion`        |    string    |    数据对象的架构版本。 发布者定义架构版本。                                                          |
|    `metadataVersion`    |    string    |    事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。    |

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。
本部分包含一个示例，介绍每个事件的数据外观。 每个事件具有以下顶级数据：

|     属性          |     类型     |     说明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    string    |    事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。                                      |
|    `subject`            |    string    |    事件主题的发布者定义路径。                                                                                              |
|    `type`          |    字符串    |    此事件源的已注册事件类型之一。                                                                                  |
|    `time`          |    字符串    |    事件的生成时间，基于提供程序的 UTC 时间。                                                                         |
|    `id`                 |    字符串    |    事件的唯一标识符。                                                                                                            |
|    `data`               |    object    |    Blob 存储事件数据。                                                                                                                    |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---

## <a name="example-events"></a>示例事件

### <a name="newkubernetesversionavailable"></a>NewKubernetesVersionAvailable

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

```json
{
    "topic": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "eventType": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "dataVersion": "1",
    "metadataVersion": "1",
    "eventTime": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```
# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

```json

{
    "source": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "type": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "specversion": "1.0",
    "time": "2021-07-01T04:52:57.0000000Z",
    "EventProcessedUtcTime": "2021-07-02T04:40:04.1049205Z",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "2021-07-01T04:52:58.8650000Z"
}
```

---

数据对象包含以下属性：

|    属性                        | 类型   | 说明                                                  |
|------------------------------------|--------|--------------------------------------------------------------|
| `latestSupportedKubernetesVersion` | string | 可用的 Kubernetes 最新支持版本。        |
| `latestStableKubernetesVersion`    | string | 可用的 Kubernetes 最新稳定支持版本。 |
| `lowestMinorKubernetesVersion`     | string | 可用的 Kubernetes 最低支持版本。        |
| `latestPreviewKubernetesVersion`   | string | 可用的 Kubernetes 最新预览版本。          |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)