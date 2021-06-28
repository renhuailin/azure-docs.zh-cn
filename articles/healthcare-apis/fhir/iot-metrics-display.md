---
title: 显示和配置适用于 FHIR 的 Azure IoT 连接器（预览版）指标
description: 本文介绍如何显示和配置 Azure IoT Connector for FHIR (预览版) 指标。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 08d572970cc96eeb28b831293b82ab6ab3257cce
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991210"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>显示和配置适用于 FHIR 的 Azure IoT 连接器（预览版）指标 

本文将了解如何显示和配置 Azure IoT Connector for 快速医疗保健互操作性资源 (FHIR&#174;) 指标。

> [!TIP]
> 若要了解如何设置指标数据的导出，请遵循通过诊断设置 导出 Azure IoT [Connector for FHIR (预览) 中的指南](iot-metrics-diagnostics-export.md)。

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>显示适用于 FHIR Azure IoT 预览 (连接器) 

1. 登录到 Azure 门户，然后选择Azure API for FHIR服务。 

2. 在左窗格中，选择"**指标"。** 

3. 选择 **"IoT Connector** 选项卡。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="&quot;IoT Connector&quot;窗格的屏幕截图，其中显示传入消息和规范化消息数的线条图。" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. 选择IoT Connector以查看其指标。 例如，有四个 IoT 连接器 (*连接器 1、* 连接器 *2* 等) 与此服务Azure API for FHIR关联。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="&quot;IoT Connector&quot;窗格的屏幕截图，其中IoT Connector选项卡 1、2、3 和 4。" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. 选择要显示的 (时间段，例如 **，1 小时****、24** 小时 **、7** 天) 自定义IoT Connector指标。 通过选择 **"自定义** "选项卡，可以创建用于显示指标的特定时间/IoT Connector组合。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="&quot;IoT Connector&quot;窗格的屏幕截图，其中显示了&quot;连接器 1&quot;的&quot;1 小时&quot;时间段线图。" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器的指标 (预览)  

> [!TIP]
> 若要了解 Azure IoT Connector for FHIR 中的数据流，请查看 [Azure IoT Connector for FHIR](iot-data-flow.md) (预览版) 数据流和 [Azure IoT Connector for FHIR (](iot-troubleshoot-guide.md) 预览版) 故障排除指南，了解有关错误消息和修复的详细信息。

下表IoT Connector可以显示的指标：

|指标类型|指标用途| 
|-----------|--------------|
|传入消息数|显示接收的原始传入消息数 (例如，设备事件) 。|
|规范化消息数|显示规范化消息的数量。|
|消息分组数|显示在指定时间窗口中聚合消息的组数。|
|平均规范化阶段延迟|显示规范化阶段的平均延迟。 规范化阶段对原始传入消息执行规范化。|
|分组阶段平均延迟|显示组阶段的平均延迟。 组阶段对规范化消息执行缓冲、聚合和分组。| 
|Total Error Count|显示错误总数。| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>专注于并配置 Azure IoT Connector for FHIR (预览版) 指标

本示例重点介绍"传入消息数 **"指标** 。

1. 选择要关注的时间点。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="&quot;传入消息数&quot;指标窗格的屏幕截图，其中突出显示了线条图上的单个时间点。" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. 在"**传入消息数**"窗格中，可以通过选择"添加指标"、"**添加** 筛选器"或"应用拆分"来进一步 **自定义指标**。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="&quot;传入消息数&quot;指标窗格的屏幕截图，其中突出显示了&quot;添加指标&quot;、&quot;添加筛选器&quot;和&quot;应用拆分&quot;按钮。" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>结论 
访问数据平面指标对于监视和故障排除至关重要。 Azure IoT Connector for FHIR 通过指标帮助你执行这些操作。 

## <a name="next-steps"></a>后续步骤

获取有关适用于 FHIR Azure IoT的常见问题的解答。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。 
