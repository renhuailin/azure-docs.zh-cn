---
title: 自动纵向扩展 Azure 事件中心吞吐量单位
description: 在命名空间上启用自动扩充，以自动纵向扩展吞吐量单位（标准层）。
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 05ea9a76ec5ece9bf522679c85f3671d600d41e8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444097"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units-standard-tier"></a>自动纵向扩展 Azure 事件中心吞吐量单位（标准层） 
Azure 事件中心是高度可缩放的数据流式处理平台。 因此，开始使用该服务后事件中心使用量通常会增加。 这样的使用量需要增加预先确定的[吞吐量单位 (TU)](event-hubs-scalability.md#throughput-units)，以扩展事件中心和处理更大的传输速率。 事件中心的“自动扩充”功能通过增加 TU 数进行自动纵向扩展，以便满足使用量需求。 增加 TU 可防止出现限制情况，这些情况包括：

* 数据入口速率超过设置 TU 
* 数据出口请求速率超过设置 TU

当负载的增加超过最小阈值时，事件中心服务会增加吞吐量，不会因服务器繁忙错误导致任何请求失败。

> [!NOTE]
> 若要详细了解高级层，请参阅[事件中心高级层](event-hubs-premium-overview.md)。

## <a name="how-auto-inflate-works-in-standard-tier"></a>自动扩充在标准层中的工作原理
事件中心流量由 TU（标准层）控制。 有关每个 TU 的入口和出口速率等限制，请参阅[事件中心配额和限制](event-hubs-quotas.md)。 利用自动扩充，可从选择的最少所需 TU 开始。 然后此功能会自动将所需 TU 增加到最大值，具体取决于增加的流量。 自动膨胀具有以下优势：

- 高效的缩放机制，可从少量开始并随流量增长而增加。
- 自动增加到指定上限，没有限制问题。
- 更好地控制缩放，因为你可控制缩放的时间和程度。

> [!NOTE]
> 当流入或流出速率低于限制时，自动扩充不会自动减少 TUS 数。 

 ## <a name="enable-auto-inflate-on-a-namespace"></a>在命名空间上启用自动膨胀
可以使用 [Azure 门户](https://portal.azure.com)或 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate)在标准层事件中心命名空间上启用或禁用自动扩充。

高级事件中心命名空间会自动启用该功能。 无法禁用它。 

> [!NOTE]
> 基本层事件中心命名空间不支持自动膨胀。

## <a name="use-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，可以在创建标准事件中心命名空间时，或创建命名空间后启用该功能。 还可以设置命名空间的 TU 并指定最大 TU 限制 

创建事件中心命名空间时，可启用自动扩充功能。 下图显示了如何为标准层命名空间启用自动扩充功能，以及如何配置开始时的 TU 和 TU 的最大数量。 

:::image type="content" source="./media/event-hubs-auto-inflate/event-hubs-auto-inflate.png" alt-text="为标准层命名空间创建事件中心时启用自动扩充的屏幕截图":::

启用此选项后，可从少量 TU 开始并随所需使用量的增长而纵向扩展。 扩充的上限不会立刻影响定价，定价取决于每小时使用的 TU 量。

若要启用自动扩充功能并修改其现有设置，请执行以下步骤：

1. 在“事件中心命名空间”页上，选择左侧菜单“设置”下的“缩放”  。
2. 在“缩放设置”  页上，选中“启用”复选框  （如果未启用自动缩放功能）。

    :::image type="content" source="./media/event-hubs-auto-inflate/scale-settings.png" alt-text="为现有标准命名空间启用自动扩充的屏幕截图":::
3. 输入吞吐量单位的 **最大** 数目或使用滚动条设置该值。
4. （可选）在此页顶部更新吞吐量单位的 **最小** 数目。

> [!NOTE]
> 当应用自动膨胀配置以增加吞吐量单位时，事件中心服务会发出诊断日志，提供有关为何以及何时增加吞吐量的信息。 若要启用事件中心的诊断日志记录，请在 Azure 门户的事件中心页上的左侧菜单上选择“诊断设置”。 有关详细信息，请参阅[设置 Azure 事件中心的诊断日志](monitor-event-hubs-reference.md#resource-logs)。


## <a name="use-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

可在 Azure 资源管理器模板部署期间启用自动膨胀。 例如，将 `isAutoInflateEnabled` 属性设置为“true”并将 `maximumThroughputUnits` 设置为 10。 例如：

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

有关完整的模板，请参阅 GitHub 上的[创建事件中心命名空间和启用膨胀模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate)。


## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](./event-hubs-about.md)
