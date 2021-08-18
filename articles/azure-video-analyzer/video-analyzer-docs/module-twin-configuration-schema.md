---
title: Azure 视频分析器模块孪生 JSON 架构
description: 本文概述了 Azure 视频分析器模块孪生 JSON 架构。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 1609552798c5c8e61c704668a8418fb6289c3c25
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602237"
---
# <a name="module-twin-configuration-schema"></a>模块孪生配置架构

设备孪生是存储设备状态信息（包括元数据、配置和条件）的 JSON 文档。 Azure IoT 中心为连接到 IoT 中心的每台设备保留一个设备孪生。 有关详细说明，请参阅[了解并在 IoT 中心内使用模块孪生](../../iot-hub/iot-hub-devguide-module-twins.md)。

本主题介绍 Azure 视频分析器的模块孪生 JSON 架构。

## <a name="module-twin-properties"></a>模块孪生属性

Azure 视频分析器公开以下模块孪生属性。

| 属性                    | 必须 | 动态 | 说明                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | 是      | 否      | 身份验证令牌，用于验证边缘模块和预配云服务（包括对视频分析器帐户的访问权限） |
| `ApplicationDataDirectory`    | 是      | 否      | 模块的文件系统中的路径，映射到已装载卷，用于保留配置。       |
| `DiagnosticsEventsOutputName` | 否       | 是     | 诊断事件的中心输出。 （空表示未发布诊断） |
| `OperationalEventsOutputName` | 否       | 是     | 操作事件的中心输出。 （空表示未发布操作事件） |
| `LogLevel`                    | 否       | 是     | 以下选项之一： · 详细 · 信息（默认） · 警告 · 错误 · 无 |
| `LogCategories`               | 否       | 是     | 以下内容的逗号分隔列表：Application、MediaPipeline、Events Default: Application、Events |
| `AllowUnsecuredEndpoints`     | 否       | 是     | 布尔设置，用于允许创建具有不安全终结点（例如 `#Microsoft.VideoAnalyzer.UnsecuredEndpoint`）的拓扑，默认值为 true        |
| `TelemetryOptOut`             | 否       | 否     | 布尔值设置，用于选择退出遥测数据提交，默认值为 false       |
| `DebugLogsDirectory`          | 否       | 是     | 调试日志的目录。 如果存在则生成日志，如果不存在则禁用调试日志。       |

无需重启模块即可更新动态属性。 

有关可选诊断设置的角色的详细信息，请参阅[监视和日志记录](monitor-log-edge.md)。

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>后续步骤

[直接方法](direct-methods.md)
