---
title: 显示和配置适用于 FHIR 的 IoT 连接器（预览版）指标
description: 本文介绍如何显示和配置适用于 FHIR 的 Azure IoT 连接器（预览版）指标。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 7c8e7a32f6e62685a11d772924db24d15d91a286
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778079"
---
# <a name="display-and-configure-iot-connector-for-fhir-preview-metrics"></a>显示和配置适用于 FHIR 的 IoT 连接器（预览版）指标 

在本文中，你将了解如何显示和配置“适用于快速医疗保健互操作性资源 (FHIR&#174;)* 的 Azure IoT 连接器”指标。

> [!TIP]
> 若要了解如何设置指标数据的导出，请按照[通过诊断设置导出“适用于 FHIR 的 Azure IoT 连接器（预览版）”指标](iot-metrics-diagnostics-export.md)中的指南进行操作。

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>显示适用于 FHIR 的 Azure IoT 连接器（预览版）的指标

1. 登录到 Azure 门户，然后选择 Azure API for FHIR 服务。 

2. 在左侧窗格中，选择“指标”。 

3. 选择“IoT 连接器”选项卡。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="“IoT 连接器”窗格的屏幕截图，其中包含显示传入消息数和规范化消息数的折线图。" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. 选择要查看其指标的 IoT 连接器。 例如，有四个 IoT 连接器（连接器 1、连接器 2 等）与此 Azure API for FHIR 服务相关联 。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="“IoT 连接器”窗格的屏幕截图，其中显示了 IoT 连接器选项卡 1、2、3 和 4。" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. 选择要显示的 IoT 连接器指标的时间段（例如 1 小时、24 小时、7 天或自定义   ）。 通过选择“自定义”选项卡，可以创建用于显示 IoT 连接器指标的特定时间/日期组合。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="“IoT 连接器”窗格的屏幕截图，其中显示“连接器 1”的“1 小时”时间段折线图。" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）的指标类型 

> [!TIP]
> 请查看[适用于 FHIR 的 Azure IoT 连接器（预览版）数据流](iot-data-flow.md)，了解适用于 FHIR 的 Azure IoT 连接器中的数据流，查看[适用于 FHIR 的 Azure IoT 连接器（预览版）故障排除指南](iot-troubleshoot-guide.md)，详细了解错误消息和修补程序。

下表列出了可显示的 IoT 连接器指标：

|指标类型|指标用途| 
|-----------|--------------|
|传入消息数|显示收到的原始传入消息的数量（例如设备事件）。|
|规范化消息数|显示规范化消息的数量。|
|消息分组数|显示在指定时间窗口内聚合了消息的组数。|
|规范化阶段平均延迟|显示规范化阶段的平均延迟。 规范化阶段对原始传入消息执行规范化。|
|分组阶段平均延迟|显示分组阶段的平均延迟。 分组阶段对规范化消息执行缓冲、聚合和分组操作。| 
|Total Error Count|显示错误总数。| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>关注和配置适用于 FHIR 的 Azure IoT 连接器（预览版）指标

在此示例中，我们重点介绍“传入消息数”指标。

1. 选择要关注的时间点。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="“传入消息数”指标窗格的屏幕截图，突出显示了折线图上的单个时间点。" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. 在“传入消息数”窗格中，可以通过选择“添加指标”、“添加筛选器”或“应用拆分”来进一步自定义指标   。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="“传入消息数”指标窗格的屏幕截图，突出显示了“添加指标”、“添加筛选器”和“应用拆分”按钮。" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>结论 
有权访问数据平面指标对于监视和故障排除至关重要。 适用于 FHIR 的 Azure IoT 连接器通过指标帮助你完成这些操作。 

## <a name="next-steps"></a>后续步骤

获取有关适用于 FHIR 的 Azure IoT 连接器的常见问题解答。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。 
