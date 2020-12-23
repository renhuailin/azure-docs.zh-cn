---
title: 显示和配置适用于 FHIR 的 Azure IoT 连接器（预览版）指标
description: 本文介绍如何显示和配置适用于 FHIR (预览版) 指标的 Azure IoT 连接器。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 79a7b18986f4bed503cd5493a7d74325a13fe535
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630509"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>显示和配置适用于 FHIR 的 Azure IoT 连接器（预览版）指标 

在本文中，你将了解如何显示和配置 Azure IoT 连接器，以便为快速医疗保健互操作性资源 (FHIR&#174;) * 度量值进行显示和配置。

> [!TIP]
> 若要了解如何设置指标数据的导出，请按照 [导出 Azure IoT 连接器 FOR FHIR 中的指南 (通过诊断设置预览) 指标](./iot-metrics-diagnostics-export.md)。

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>显示适用于 FHIR 的 Azure IoT Connector 的指标 (预览版) 

1. 登录到 Azure 门户，然后选择 Azure API for FHIR 服务。 

2. 在左侧窗格中，选择 " **指标** "。 

3. 选择 " **IoT 连接器** " 选项卡。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="&quot;IoT 连接器&quot; 窗格的屏幕截图，具有显示传入消息和规范化消息数的折线图。" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. 选择 IoT 连接器以查看其指标。 例如，有四个 IoT 连接器 ( *连接器 1* 、 *连接器 2* 等) 与此 Azure API for FHIR 服务相关联。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="&quot;IoT 连接器&quot; 窗格的屏幕截图，显示 IoT 连接器选项卡1、2、3和4。" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. 选择时间段 (例如， **1 小时** 、 **24 小时** 、 **7 天** 或要显示的 IoT 连接器指标的 **自定义** ) 。 通过选择 " **自定义** " 选项卡，可以创建用于显示 IoT 连接器指标的特定时间/日期组合。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="&quot;IoT 连接器&quot; 窗格的屏幕截图，显示 &quot;Connector 1&quot; 的时间段的 &quot;1 小时&quot; 行图。" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT Connector 的指标类型 (预览版)  

> [!TIP]
> 若要了解 Azure IoT Connector for FHIR 中的数据流，请查看适用于 [FHIR 的 Azure Iot 连接器 (预览) ](./iot-data-flow.md) 数据流和 [用于 FHIR 的 azure iot 连接器 (预览) 故障排除指南](./iot-troubleshoot-guide.md) ，详细了解错误消息和修补程序。

下表列出了可以显示的 IoT 连接器指标：

|度量值类型|指标用途| 
|-----------|--------------|
|传入消息数|显示收到的原始传入消息数 (例如，设备事件) 。|
|规范化消息数|显示规范化消息的数量。|
|消息组数|显示在指定的时间范围内聚合了消息的组的数目。|
|平均标准化阶段延迟|显示规范化阶段的平均延迟。 规范化阶段对原始传入消息执行规范化。|
|平均组阶段延迟|显示组阶段的平均延迟。 组阶段对规范化消息执行缓冲、聚合和分组。| 
|Total Error Count|显示错误的总数。| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>专注于 FHIR 的 Azure IoT 连接器 (预览) 指标

在此示例中，我们将重点介绍 **传入消息的** 度量值。

1. 选择要关注的时间点。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="&quot;传入消息数&quot; 指标窗格的屏幕截图，在折线图上突出显示单个时间点。" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. 在 " **传入消息数** " 窗格中，通过选择 " **添加度量值** "、" **添加筛选器** " 或 " **应用拆分** "，可以进一步自定义度量值。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="&quot;传入消息数&quot; 指标窗格的屏幕截图，突出显示 &quot;添加度量值&quot;、&quot;添加筛选器&quot; 和 &quot;应用拆分&quot; 按钮。" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>结论 
有权访问数据平面指标对于监视和故障排除至关重要。 适用于 FHIR 的 Azure IoT 连接器通过指标帮助你执行这些操作。 

## <a name="next-steps"></a>后续步骤

获取有关 Azure IoT Connector for FHIR 的常见问题的解答。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器常见问题](fhir-faq.md)

* 在 Azure 门户中，用于 FHIR 的 Azure IoT 连接器称为 IoT 连接器 (预览版) 。 FHIR 是 HL7 的注册商标，用于 HL7 的权限。 
