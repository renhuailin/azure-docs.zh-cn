---
title: 通过“诊断”设置导出 Azure IoT Connector for FHIR（预览版）指标
description: 本文介绍如何通过诊断设置导出适用于 FHIR (preview) 指标的 Azure IoT 连接器
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 00abad784048b67e9d89c12b9be3f631f586fb07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574532"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>通过“诊断”设置导出 Azure IoT Connector for FHIR（预览版）指标

在本文中，你将了解如何为快速医疗保健互操作性资源（ (FHIR&#174;) * 指标日志）导出 Azure IoT 连接器。 启用指标日志记录的功能是 Azure 门户中的 [**诊断设置**](../azure-monitor/essentials/diagnostic-settings.md) 。 

> [!TIP]
> 按照在用于 [FHIR 的 AZURE API 和用于 FHIR 的 Azure IoT 连接器中的启用诊断日志记录中](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) 的指南设置审核日志记录。

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>为适用于 FHIR 的 Azure IoT 连接器启用指标日志记录 (预览) 
1. 若要为 FHIR 的 Azure IoT 连接器启用指标日志记录，请在 Azure 门户中选择用于 FHIR 服务的 Azure API 

2. 导航到 "**诊断设置**" 

3. 选择“+ 添加诊断设置”

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. 在 " **诊断设置名称** " 对话框中输入名称。

5. 选择要用于访问诊断日志的方法：

    1. **存档到存储帐户** 进行审核或手动检查。 需要使用的存储帐户需要已创建。
    2. **流式传输到事件中心** ，以供第三方服务或自定义分析解决方案引入。 你需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. **流到 Azure Monitor 中的 Log Analytics** 工作区。 你需要先创建日志分析工作区，然后才能选择此选项。

6. 为 FHIR 的 Azure IoT 连接器选择 " **错误"、"流量" 和 "延迟** "。  选择要为 Azure API for FHIR 捕获的任何其他度量值类别。

7. 选择“保存”

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 最多可能需要15分钟才能在所选存储库中显示第一个指标日志。  
 
有关如何使用诊断日志的详细信息，请参阅 [Azure 资源日志文档](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>结束语 
对度量值日志的访问权限对于监视和故障排除至关重要。  使用适用于 FHIR 的 Azure IoT 连接器，可以通过指标日志执行这些操作。 

## <a name="next-steps"></a>后续步骤

查看有关适用于 FHIR 的 Azure IoT 连接器的常见问题。

>[!div class="nextstepaction"]
>[适用于 FHIR 常见问题的 Azure IoT 连接器](fhir-faq.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。