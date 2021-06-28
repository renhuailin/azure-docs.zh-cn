---
title: 通过“诊断”设置导出 Azure IoT Connector for FHIR（预览版）指标
description: 本文介绍如何通过诊断设置导出 Azure IoT Connector for FHIR (预览) 指标
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: efdf932a26e783ff4e7fc22820e061fb9b2e26ba
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992146"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>通过“诊断”设置导出 Azure IoT Connector for FHIR（预览版）指标

本文将了解如何导出适用于 Azure IoT FHIR 快速医疗保健互操作性资源 (* 指标&#174;) 连接器。 启用指标日志记录的功能是"诊断"[**设置Azure 门户。**](../../azure-monitor/essentials/diagnostic-settings.md) 

> [!TIP]
> 按照在适用于 [FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) 的 Azure API for FHIR Azure IoT 连接器中启用诊断日志记录中的指导设置审核日志记录。

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>为适用于 FHIR 的 Azure IoT 连接器启用指标日志记录 (预览) 
1. 若要为适用于 FHIR 的 Azure IoT 连接器启用指标日志记录，请在Azure API for FHIR中选择Azure 门户 

2. 导航到 **"诊断设置"** 

3. 选择“+ 添加诊断设置”

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT 连接器1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. 在"诊断设置 **名称"对话框中输入** 名称。

5. 选择要用于访问诊断日志的方法：

    1. **存档到存储帐户进行** 审核或手动检查。 需要已创建想要使用的存储帐户。
    2. **流式传输到** 事件中心，供第三方服务或自定义分析解决方案进行集成。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. **流式传输到 Azure Monitor** 中的 Log Analytics 工作区。 需要先创建日志分析工作区，然后才能选择此选项。

6. 为 **FHIR 的** Azure IoT 连接器选择"错误、流量和延迟"。  选择要为资源组捕获的其他任何指标Azure API for FHIR。

7. 选择“保存”

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT 连接器 2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 第一个指标日志最多可能需要 15 分钟才能显示在你选择的存储库中。  
 
若要详细了解如何使用诊断日志，请参阅 [Azure 资源日志文档](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>结论 
有权访问指标日志对于监视和故障排除至关重要。  Azure IoT FHIR 连接器允许通过指标日志执行这些操作。 

## <a name="next-steps"></a>后续步骤

查看有关适用于 FHIR Azure IoT连接器的常见问题解答。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。