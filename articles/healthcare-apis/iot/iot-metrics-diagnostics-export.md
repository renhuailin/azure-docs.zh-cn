---
title: 通过诊断设置导出 IoT 连接器指标-Azure 医疗保健 Api
description: 本文介绍如何通过诊断设置导出 IoT 连接器指标
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 09/30/2021
ms.author: jasteppe
ms.openlocfilehash: 23908c8ab910324cda4cd2802158447a1c97db4e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354591"
---
# <a name="export-iot-connector-metrics-through-diagnostic-settings"></a>通过诊断设置导出 IoT 连接器指标

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍如何导出 IoT 连接器指标日志。 用于启用指标日志记录的功能是 Azure 门户中的[诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)。 

## <a name="enable-metrics-logging-for-iot-connector"></a>为 IoT 连接器启用指标日志记录
1. 若要为 IoT 连接器启用指标日志记录，请在 Azure 门户中选择快速医疗保健互操作性资源 (FHIR&#174;) 服务。 

2. 导航到诊断设置 

3. 选择“+ 添加诊断设置”

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. 在“诊断设置名称”对话框中输入名称。

5. 选择要用于访问诊断日志的方法：

    1. 将诊断日志保存到存储帐户以便审核或手动检查。 需要已创建想要使用的存储帐户。
    2. 流式传输到事件中心，以便第三方服务或自定义分析解决方案引入。 需要先创建事件中心命名空间和事件中心策略，然后才能配置此步骤。
    3. 流式传输到 Azure Monitor 中的 Log Analytics 工作区。 需要先创建 Logs Analytics 工作区，然后才能选择此选项。

6. 为 IoT 连接器选择 **"错误"、"流量" 和 "延迟"** 。  选择要为 FHIR 服务捕获的任何额外指标类别。

7. 选择“保存”

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 第一批指标日志可能需要 15 分钟才能显示在所选存储库中。  
 
若要详细了解如何使用诊断日志，请参阅 [Azure 资源日志文档](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="conclusion"></a>结论 
有权访问指标日志对于监视和故障排除至关重要。  IoT 连接器允许您通过指标日志执行这些操作。 

## <a name="next-steps"></a>后续步骤

查看有关 IoT 连接器的常见问题。

>[!div class="nextstepaction"]
>[IoT 连接器常见问题](../fhir/fhir-faq.md)

 (FHIR&#174;) 是 HL7 的注册商标，并与 HL7 的权限一起使用。