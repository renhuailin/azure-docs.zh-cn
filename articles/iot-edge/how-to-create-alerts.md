---
title: 使用警报获取有关问题的通知 - Azure IoT Edge
description: 使用 Azure Monitor 警报规则进行大规模监视
author: veyalla
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1de507f113f5bf457a4394be9e5fb86c37291e95
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015485"
---
# <a name="get-notified-about-issues-using-alerts-preview"></a>使用警报获取有关问题的通知（预览版）

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

使用 [Azure Monitor 日志警报](../azure-monitor/alerts/alerts-unified-log.md)可以大规模监视 IoT Edge 设备。 [解决方案体系结构](how-to-collect-and-transport-metrics.md#architecture)中强调过，Azure Monitor Log Analytics 用作指标数据库。 这种集成利用以资源为中心的日志警报，解锁功能强大且灵活的警报功能。

> [!IMPORTANT]
> 此功能目前仅适用于 IoT 中心，不适用于 IoT Central。

## <a name="create-an-alert-rule"></a>创建警报规则

可以[创建日志警报规则](../azure-monitor/alerts/alerts-log.md)，用于监视整个设备组中的各种状况。

IoT 中心资源下提供了示例 [KQL](/azure/data-explorer/kusto/query/) 警报查询。 对来自边缘设备的指标数据进行操作的查询，在其标题中以“IoT Edge:”为前缀。 请按原样使用这些示例，或根据需要对其进行修改，以创建可满足你确切需求的查询。

若要访问示例警报查询，请使用以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 在菜单的“监视”部分中，选择“日志” 。
1. 选择“查询”以打开示例查询浏览器。

:::image type="content" source="./media/how-to-create-alerts/example-alerts.png" alt-text="访问示例警报查询。" lightbox="./media/how-to-create-alerts/example-alerts.png":::

[指标收集器模块](how-to-collect-and-transport-metrics.md#metrics-collector-module)将所有数据都引入标准 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 表中。 你可以通过查询同一个表，基于自定义模块中的指标数据创建警报规则。

### <a name="split-by-device-dimension"></a>按设备维度拆分

所有示例警报规则都按设备 ID 查询聚合值。 需要此分组来确定是哪个设备导致了警报的发生。 可以选择特定设备来启用警报规则，也可以在所有设备上启用。 在设置警报逻辑之前，使用预览图浏览每个设备的趋势。

### <a name="choose-notification-preferences"></a>选择通知首选项

创建警报规则时，在[操作组](../azure-monitor/alerts/action-groups.md)中配置通知首选项并将其与警报规则关联。

## <a name="select-alert-rule-scope"></a>选择警报规则范围

使用上一部分中的指南创建范围限定为单个 IoT 中心的警报规则。 但是，你可能想要为多个 IoT 中心创建相同的规则。 将范围更改为资源组或整个订阅，以便在该范围内的所有中心上启用警报规则。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 在菜单的“监视”部分中，选择“日志” 。
1. 选择“选择范围”以更改警报规则的范围。

:::image type="content" source="./media/how-to-create-alerts/change-scope.png" alt-text="更改警报范围" lightbox="./media/how-to-create-alerts/change-scope.png":::

按 `_ResourceId` 字段聚合值，在创建警报规则时，选择它作为“资源 ID 列”。 为方便起见，此方法将警报与正确的资源相关联。

## <a name="viewing-alerts"></a>查看警报

在 [IoT Edge 队列视图工作簿](how-to-explore-curated-visualizations.md#iot-edge-fleet-view-workbook)的“警报”选项卡中，查看为多个 IoT 中心的设备生成的警报。

单击警报规则名称可以查看有关警报的更多上下文。 单击设备名称链接会显示在触发警报前后，设备的详细指标。

## <a name="next-steps"></a>后续步骤

使用[自定义模块中的指标](how-to-add-custom-metrics.md)增强监视解决方案。