---
title: 通过“诊断”设置导出 Azure IoT Connector for FHIR（预览版）指标
description: 本文介绍如何通过“诊断”设置导出面向 FHIR 的 Azure IoT 连接器（预览版）指标
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 9e0257ad4b4f5887adee724668b227ba87c55e29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778078"
---
# <a name="export-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>通过“诊断”设置导出面向 FHIR 的 Azure IoT 连接器（预览版）指标

本文将介绍如何导出面向快速医疗保健互操作性资源 (FHIR&#174;) 的 Azure IoT 连接器指标日志。 用于启用指标日志记录的功能是 Azure 门户中的[诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。 

> [!TIP]
> 按照[在面向 FHIR 的 Azure API 和面向 FHIR 的 Azure IoT 连接器中启用诊断日志](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir)的指导，设置审核日志记录。

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>为面向 FHIR 的 Azure IoT 连接器（预览版）启用指标日志记录
1. 若要为面向 FHIR 的 Azure IoT 连接器启用指标日志记录，请在 Azure 门户中选择面向 FHIR 服务的 Azure API 

2. 导航到诊断设置 

3. 选择“+ 添加诊断设置”

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. 在“诊断设置名称”对话框中输入名称。

5. 选择要用于访问诊断日志的方法：

    1. 将诊断日志保存到存储帐户以便审核或手动检查。 需要已创建想要使用的存储帐户。
    2. 流式传输到事件中心，以便第三方服务或自定义分析解决方案引入。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. 流式传输到 Azure Monitor 中的 Log Analytics 工作区。 需要先创建 Logs Analytics 工作区，然后才能选择此选项。

6. 为面向 FHIR 的 Azure IoT 连接器选择“错误、流量和延迟”。  选择要为面向 FHIR 的 Azure API 捕获的任何其他指标类别。

7. 选择“保存”

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 第一批指标日志可能需要 15 分钟才能显示在所选存储库中。  
 
若要详细了解如何使用诊断日志，请参阅 [Azure 资源日志文档](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>结论 
有权访问指标日志对于监视和故障排除至关重要。  有了面向 FHIR 的 Azure IoT 连接器，你就可以通过指标日志执行这些操作。 

## <a name="next-steps"></a>后续步骤

查看有关面向 FHIR 的 Azure IoT 连接器的常见问题解答。

>[!div class="nextstepaction"]
>[面向 FHIR 的 Azure IoT 连接器的常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。