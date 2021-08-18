---
title: 设备与 Azure IoT Central 连接故障排除 | Microsoft Docs
description: 排查在 IoT Central 中无法看到设备数据的原因
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: faeaf58537da4a40716f0c2e76b205980b727bf9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459099"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>排查设备中的数据未在 Azure IoT Central 中显示的原因

本文档有助于找出设备发送到 IoT Central 的数据未显示在应用程序中的原因。

要调查以下两个主要方面：

- 设备连接问题
  - 身份验证问题，比如设备的凭据无效
  - 网络连接问题
  - 设备未或的批准或被阻止
- 设备有效负载形状问题

本故障排除指南重点介绍设备连接问题和设备有效负载形状问题。

## <a name="device-connectivity-issues"></a>设备连接问题

本部分可帮助确定你的数据是否可以到达 IoT Central。

安装 `az cli` 工具和 `azure-iot` 扩展（如果尚未安装）。

要了解如何安装 `az cli`，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

要[安装](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) `azure-iot` 扩展，请运行以下命令：

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> 首次运行扩展命令时，系统可能会提示您安装 `uamqp` 库。

安装 `azure-iot` 扩展后，请启动设备，查看设备发送的消息是否能够到达 IoT Central。

使用以下命令登录到你的 IoT Central 应用程序的订阅：

```azurecli
az login
az account set --subscription <your-subscription-id>
```

要监视设备正在发送的遥测数据，请使用以下命令：

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

如果设备已成功连接到 IoT Central，则会看到类似于以下内容的输出：

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

要监视设备与 IoT Central 交换的属性更新，请使用以下预览命令：

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

如果设备成功发送属性更新，将会看到类似于以下内容的输出：

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

如果你看到数据显示在终端中，则表明数据能够到达你的 IoT Central 应用程序。

如果在几分钟后看不到任何数据，请尝试按键盘上的 `Enter` 或 `return` 键，以防输出停滞。

如果终端上仍未显示任何数据，则可能是设备出现网络连接问题，或者没有正确地将数据发送到 IoT Central。

### <a name="check-the-provisioning-status-of-your-device"></a>检查设备的预配状态

如果监视器上未显示数据，请运行以下命令检查设备的预配状态：

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

以下输出显示了导致设备无法连接的示例：

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| 设备预配状态 | 说明 | 可能的缓解操作 |
| - | - | - |
| 已预配 | 无立即可识别的问题。 | 空值 |
| 已注册 | 设备尚未连接到 IoT Central。 | 检查设备日志以确认连接问题。 |
| 已阻止 | 设备被阻止连接到 IoT Central。 | 设备被阻止连接到 IoT Central 应用程序。 在 IoT Central 中取消阻止设备，然后重试。 要了解详细信息，请参阅[阻止设备](concepts-get-connected.md#device-status-values)。 |
| 未获得批准 | 设备未获得批准。 | 设备未获得连接到 IoT Central 应用程序的批准。 在 IoT Central 中批准设备，然后重试。 要了解详细信息，请参阅[批准设备](concepts-get-connected.md#device-registration) |
| 未关联 | 设备未与设备模板关联。 | 将设备与设备模板关联，以便 IoT Central 知道如何分析数据。 |

了解有关[设备状态代码](concepts-get-connected.md#device-status-values)的详细信息。

### <a name="error-codes"></a>错误代码

如果仍无法诊断 `monitor-events` 中不显示数据的原因，下一步是查找设备报告的错误代码。

在设备上启动调试会话，或从设备收集日志。 检查设备报告的任何错误代码。

下表显示了常见错误代码和可能有效的缓解操作。

如果你看到与身份验证流相关的问题：

| 错误代码 | 说明 | 可能的缓解操作 |
| - | - | - |
| 400 | 请求的正文无效。 例如，无法进行分析，或者对象无法验证。 | 请确保在证明流中发送正确的请求正文，或者使用设备 SDK。 |
| 401 | 无法验证授权令牌。 例如，令牌已过期或不适用于请求的 URI。 此错误代码也会在 TPM 证明流中返回给设备。 | 确保你的设备具有正确的凭据。 |
| 404 | 设备预配服务实例或某个资源（如注册）不存在。 | [向客户支持提交票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 |
| 412 | 根据 RFC7232 规范，请求中的 `ETag` 与现有资源中的 `ETag` 不匹配。 | [向客户支持提交票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 |
| 429 | 服务限制了操作。 对于具体的服务限制，请参阅 [IoT 中心设备预配服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)。 | 减少消息频率，将职责分给更多设备。 |
| 500 | 发生内部错误。 | [向客户支持部门提交票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)，看看客户支持部门能否进一步提供帮助。 |

### <a name="file-upload-error-codes"></a>文件上传错误代码

下面是在设备尝试将文件上传到云时可能会看到的常见错误代码的列表。 请记住，在设备可以上传文件之前，必须先在应用程序中配置[设备文件上传](howto-configure-file-uploads.md)。

| 错误代码 | 说明 | 可能的缓解操作 |
| - | - | - |
| 403006  | 已超出并发文件上传操作的数量。 每个设备客户端仅限 10 个并发文件上传。 | 确保设备立即通知 IoT Central 文件上传操作已完成。 如果这不起作用，请尝试减少请求超时。 |

## <a name="payload-shape-issues"></a>有效负载形状问题

确认你的设备将数据发送到 IoT Central 后，下一步是确保你的设备以有效格式发送数据。

如下两种常见问题类别会导致设备数据不会出现在 IoT Central 中：

- 设备模板与设备数据不匹配：
  - 命名不匹配，如输入错误或大小写匹配问题。
  - 未建模属性，其中的架构未在设备模板中定义。
  - 架构不匹配，比如，某一类型在模板中定义为 `boolean`，但数据为字符串。
  - 同一遥测数据名称在多个接口中定义，但设备不符合插即用符合 IoT 规范。
- 数据形状是无效的 JSON。 要了解详细信息，请参阅[遥测、属性和命令有效负载](concepts-telemetry-properties-commands.md)。

要检测问题的类别，请针对你的情况运行最适合的命令：

- 要验证遥测数据，请使用预览命令：

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- 要验证属性更新，请使用预览命令

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

首次运行 `validate` 命令时，系统可能会提示您安装 `uamqp` 库。

以下输出显示了来自验证命令的示例错误和警告消息：

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

如果希望使用 GUI，请使用 IoT Central 的“原始数据”视图，查看是否有些内容没有建模。 “原始数据”视图不会检测设备发送的 JSON 是否格式有误。

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="“原始数据”视图的屏幕截图":::

检测到问题后，你可能需要更新设备固件，或创建一个新的设备模板来对以前未建模的数据进行建模。

如果选择创建可正确对数据建模的新模板，请将设备从旧模板迁移到新模板。 要了解详细信息，请参阅“[在 Azure IoT Central 应用程序中管理设备](howto-manage-devices-individually.md)”。

## <a name="next-steps"></a>后续步骤

如果需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/community/)上的 Azure 专家。 或者，你也可以提交 [Azure 支持票证](https://portal.azure.com/#create/Microsoft.Support)。

有关详细信息，请参阅 [Azure IoT 支持和帮助选项](../../iot-fundamentals/iot-support-help.md)。
