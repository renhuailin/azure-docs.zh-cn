---
title: 通过诊断设置导出 IoT 连接器指标
description: 本文介绍如何通过诊断设置导出 IoT 连接器指标
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 392ec10d98a4359d222355d083df0847ecf380b7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777810"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>通过诊断设置导出 IoT 连接器指标

本文将介绍如何导出用于快速医疗保健互操作性资源 (FHIR&#174;)* 的 Azure IoT 连接器的指标日志。 在 Azure 门户中用于启用指标日志记录的功能是[诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。 

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>为适用于 FHIR 的 Azure IoT 连接器（预览版）启用指标日志记录
1. 若要为适用于 FHIR 的 Azure IoT 连接器启用指标日志记录，请在 Azure 门户中选择你的 FHIR 服务 

2. 导航到“诊断设置” 

3. 选择“+ 添加诊断设置”

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT 连接器 1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. 在“诊断设置名称”对话框中输入名称。

5. 选择要用于访问诊断日志的方法：

    1. 存档到存储帐户以进行审核或手动检查。 你要使用的存储帐户必须已创建好。
    2. 流式传输到事件中心，以便第三方服务或自定义分析解决方案引入。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. 流式传输到 Azure Monitor 中的 Log Analytics 工作区。 需要先创建 Logs Analytics 工作区，然后才能选择此选项。

6. 为适用于 FHIR 的 Azure IoT 连接器选择“错误、流量和延迟”。  选择要为 FHIR 服务捕获的任何其他指标类别。

7. 选择“保存”

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT 连接器 2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 第一批指标日志可能需要长达 15 分钟的时间才能显示在所选存储库中。  
 
若要详细了解如何使用诊断日志，请参阅 [Azure 资源日志文档](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>结论 
有权访问指标日志对于监视和故障排除至关重要。  有了适用于 FHIR 的 Azure IoT 连接器，你就可以通过指标日志执行这些操作。 

## <a name="next-steps"></a>后续步骤

查看与适用于 FHIR 的 Azure IoT 连接器相关的常见问题解答。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器常见问题解答](../fhir/fhir-faq.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。