---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: aebee9b2511e3616a9170d5ed84be3acf391b6ad
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071962"
---
# <a name="get-connected-to-azure-iot-central"></a>连接到 Azure IoT Central

本文介绍设备如何连接到 Azure IoT Central 应用程序。 在设备可以与 IoT Central 交换数据之前，必须执行以下操作：

- 身份验证。 使用 IoT Central 应用程序进行身份验证时，将使用共享访问签名 (SAS) 令牌或 X.509 证书。 在生产环境中，建议使用 X.509 证书。
- 注册。 设备必须注册到 IoT Central 应用程序。 可以在应用程序的“设备”页上查看已注册的设备。
- 与设备模板关联。 在 IoT Central 应用程序中，设备模板定义操作员用来查看和管理连接设备的 UI。

IoT Central 支持以下两种设备注册方案：

- 自动注册。 设备首次连接时自动注册。 该方案可让 OEM 大规模制造出可在不事先注册的情况下实现连接的设备。 OEM 生成合适的设备凭据，并在工厂中配置设备。 或者，可以要求操作员在设备开始发送数据之前对其进行批准。 该方案要求在应用程序中配置 X.509 或 SAS 组注册。
- 手动注册。 操作员可以在“设备”页上注册单个设备，也可以[导入 CSV 文件](howto-manage-devices-in-bulk.md#import-devices)以批量注册设备。 在该方案中，可以使用 X.509 或 SAS 组注册，或使用 X.509 或 SAS 单个注册。

连接到 IoT Central 的设备应遵循 IoT 即插即用约定。 其中一项约定是设备在连接时应发送它所实现的设备模型的模型 ID。 模型 ID 使 IoT Central 应用程序能够将设备与正确的设备模板关联。

IoT Central 使用 [Azure IoT 中心设备预配服务 (DPS)](../../iot-dps/about-iot-dps.md) 来管理连接过程。 设备首先连接到 DPS 终结点，以检索设备连接到应用程序所需的信息。 在内部，IoT Central 应用程序使用 IoT 中心来处理设备连接。 使用 DPS：

- 可让 IoT Central 支持大规模的设备加入和连接。
- 可让你离线生成设备凭据并配置设备，而无需通过 IoT Central UI 注册设备。
- 可让你使用自己的设备 ID 在 IoT Central 中注册设备。 使用自己的设备 ID 可以简化与现有后端办公系统的集成。
- 可以通过一致的单一方式将设备连接到 IoT Central。

本文介绍以下设备连接步骤：

- [X.509 组注册](#x509-group-enrollment)
- [SAS 组注册](#sas-group-enrollment)
- [单独注册](#individual-enrollment)
- [设备注册](#device-registration)
- [将设备与设备模板关联](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X.509 组注册

在生产环境中，建议使用 X.509 证书作为 IoT Central 的设备身份验证机制。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](../../iot-hub/iot-hub-x509ca-overview.md)。

要将具有 X.509 证书的设备连接到应用程序：

1. 创建使用证书 (X.509) 证明类型的注册组。
1. 在注册组中添加并验证中间或根 X.509 证书。
1. 从注册组中的根证书或中间证书生成叶证书。 当设备连接到应用程序时，从该设备发送叶证书。

有关详细信息，请参阅[如何连接具有 X.509 证书的设备](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>仅用于测试目的

（仅限测试）可以使用以下实用工具来生成根证书、中间证书和设备证书：

- [适用于 Azure IoT 设备预配设备 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)：可用于生成和验证 X.509 证书和密钥的 Node.js 工具的集合。
- 如果使用 DevKit 设备，则此[命令行工具](https://aka.ms/iotcentral-docs-dicetool)会生成一个 CA 证书，可将其添加到 IoT Central 应用程序来验证证书。
- [管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)：PowerShell 和 Bash 脚本的集合，用于：
  - 创建证书链。
  - 将证书保存为 .cer 文件，以上传到 IoT Central 应用程序。
  - 使用 IoT Central 应用程序中的验证码生成验证证书。
  - 在工具中使用设备 ID 作为参数，为设备创建叶证书。

## <a name="sas-group-enrollment"></a>SAS 组注册

要将具有设备 SAS 密钥的设备连接到应用程序，请执行以下操作：

1. 创建使用共享访问签名 (SAS) 证明类型的注册组。
1. 从注册组中复制组主密钥或辅助密钥。
1. 使用 Azure CLI 从组密钥生成设备密钥：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 当设备连接到 IoT Central 应用程序时，使用生成的设备密钥。

## <a name="individual-enrollment"></a>单独注册

连接设备的每个客户都有自己的身份验证凭据，使用单个注册。 单个注册是允许单个设备连接的入口。 单个注册可使用 X.509 叶证书或 SAS 令牌（来自物理或虚拟受信任的平台模块）作为证明机制。 设备 ID 中可以包含字母、数字和 `-` 字符。 有关详细信息，请参阅 [DPS 单个注册](../../iot-dps/concepts-service.md#individual-enrollment)。

> [!NOTE]
> 为设备创建单个注册时，其优先级高于 IoT Central 应用程序中的默认组注册选项。

### <a name="create-individual-enrollments"></a>创建单个注册

IoT Central 支持以下单个注册证明机制：

- 对称密钥证明：对称密钥证明是使用 DPS 实例验证设备的简单方法。 若要创建使用对称密钥的单个注册，请打开设备的“设备连接”页，选择“单个注册”作为连接方法，并选择“共享访问签名 (SAS)”作为机制。 输入 base64 编码的主密钥和辅助密钥，并保存所做的更改。 使用 ID 范围、设备 ID，以及主密钥或辅助密钥来连接设备 。

    > [!TIP]
    > 如果用于测试，可以使用 OpenSSL 生成 base64 编码的密钥：`openssl rand -base64 64`

- X.509 证书：若要使用 X.509 证书创建单个注册，请打开“设备连接”页，选择“单个注册”作为连接方法，并选择“证书 (X.509)”作为机制。 与单个注册条目一起使用的设备证书要求将证书颁发者和使用者 CN 设置为设备 ID。

    > [!TIP]
    > 如果用于测试，可以使用[适用于 Node.js 的 Azure IoT 设备预配设备 SDK 工具](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)来生成自签名证书：`node create_test_cert.js device "mytestdevice"`

- 受信任的平台模块 (TPM) 证明：[TPM](../../iot-dps/concepts-tpm-attestation.md) 是一种硬件安全模块。 使用 TPM 是连接设备最安全的方法之一。 本文假定你使用单独的、固件式的或集成式的 TPM。 软件模拟 TPM 适用于原型制作或测试，但其提供的安全级别不同于单独的、固件式的或集成式的 TPM。 请勿在生产环境中使用软件 TPM。 若要创建使用 TPM 的单个注册，请打开“设备连接”页，选择“单个注册”作为连接方法，并选择“TPM”作为机制。 输入 TPM 认可密钥并保存设备连接信息。

## <a name="device-registration"></a>设备注册

设备连接到 IoT Central 应用程序之前，必须在应用程序中注册：

- 设备首次连接时可自动注册。 若要使用此选项，必须使用 [X.509 组注册](#x509-group-enrollment) 或 [SAS 组注册](#sas-group-enrollment)。
- 操作员可以导入 CSV 文件以批量注册应用程序中的设备列表。
- 操作员可以在应用程序的“设备”页上手动注册单个设备。

IoT Central 使 OEM 能够批量制造可自动注册的设备。 OEM 生成合适的设备凭据，并在工厂中配置设备。 当客户首次打开设备时，设备将连接到 DPS，DPS 随后自动将设备连接到正确的 IoT Central 应用程序。 或者，可以要求操作员在设备开始向应用程序发送数据之前批准该设备。

> [!TIP]
> 在“管理”>“设备连接”页上，“自动批准”选项控制操作员是否必须手动批准设备，然后才能开始发送数据。

### <a name="automatically-register-devices-that-use-x509-certificates"></a>自动注册使用 X.509 证书的设备

1. 使用已添加到 [X.509 注册组](#x509-group-enrollment)的根证书或中间证书为设备生成叶证书。 使用设备 ID 作为叶证书中的 `CNAME`。 设备 ID 中可以包含字母、数字和 `-` 字符。

1. 作为 OEM，使用设备 ID、生成的 X.509 叶证书和应用程序 ID 范围值刷新每台设备。 设备代码还应发送它所实现的设备模型的模型 ID。

1. 打开设备时，设备首先连接到 DPS 以检索其 IoT Central 连接信息。

1. 设备使用来自 DPS 的信息连接到 IoT Central 应用程序并向其注册。

IoT Central 应用程序使用设备发送的模型 ID [将注册的设备与设备模板关联](#associate-a-device-with-a-device-template)。

### <a name="automatically-register-devices-that-use-sas-tokens"></a>自动注册使用 SAS 令牌的设备

1. 从“SAS-IoT-Devices”注册组中复制组主密钥：

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="SAS-IoT-Devices 注册组中的组主密钥":::

1. 使用 `az iot central device compute-device-key` 命令生成设备 SAS 密钥。 使用前面步骤中获取的组主密钥。 设备 ID 中可以包含字母、数字和 `-` 字符：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 作为 OEM，使用设备 ID、生成的设备 SAS 密钥和应用程序 ID 范围值刷新每台设备。 设备代码还应发送它所实现的设备模型的模型 ID。

1. 打开设备时，它首先会连接到 DPS 以检索其 IoT Central 注册信息。

1. 设备使用来自 DPS 的信息连接到 IoT Central 应用程序并向其注册。

IoT Central 应用程序使用设备发送的模型 ID [将注册的设备与设备模板关联](#associate-a-device-with-a-device-template)。

### <a name="bulk-register-devices-in-advance"></a>提前批量注册设备

若要将大量设备注册到 IoT Central 应用程序，请使用 CSV 文件[导入设备 ID 和设备名称](howto-manage-devices-in-bulk.md#import-devices)。

如果设备使用 SAS 令牌进行身份验证，请[从 IoT Central 应用程序导出 CSV 文件](howto-manage-devices-in-bulk.md#export-devices)。 导出的 CSV 文件包括设备 ID 和 SAS 密钥。

如果设备使用 X.509 证书进行身份验证，请使用上载到 X.509 注册组的根证书或中间证书，为设备生成 X.509 叶证书。 使用导入的设备 ID 作为叶证书中的 `CNAME` 值。

设备必须为应用程序使用“ID 范围”值，并在连接时发送模型 ID。

> [!TIP]
> 可以在“管理”>“设备连接”中找到“ID 范围”值 。

### <a name="register-a-single-device-in-advance"></a>提前注册单个设备

在体验 IoT Central 或测试设备时，此方法非常有用。 在“设备”页上选择“+ 新建” 注册单个设备。 可以使用设备连接 SAS 密钥将设备连接到 IoT Central 应用程序。 从已注册设备的连接信息中复制设备 SAS 密钥：

![单个设备的 SAS 密钥](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>将设备与设备模板关联

当设备连接时，IoT Central 会自动将设备与设备模板关联。 设备连接时会发送[模型 ID](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id)。 IoT Central 使用模型 ID 识别该特定设备模型的设备模板。 发现过程如下所述：

1. 如果设备模板已在 IoT Central 应用程序中发布，则设备将与设备模板关联。
1. 如果设备模板未在 IoT Central 应用程序中发布，IoT Central 会在[公共模型存储库](https://github.com/Azure/iot-plugandplay-models)中查找设备模型。 如果 IoT Central 找到模型，它将使用该模型生成基本设备模板。
1. 如果 IoT Central 在公共模型存储库中找不到模型，则设备将被标记为“未关联”。 操作员可以为设备创建设备模板，然后将未关联的设备迁移到新设备模板，或基于设备发送的数据[自动生成设备模板](howto-set-up-template.md#autogenerate-a-device-template)。

以下屏幕截图演示了如何在 IoT Central 中查看设备模板的模型 ID。 在设备模板中，选择一个组件，然后选择“编辑标识”：

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="显示恒温器设备模板中的模型 ID 的屏幕截图。":::

可以在公共模型存储库中查看[恒温器模型](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json)。 模型 ID 定义如下所示：

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>设备状态值

当真实设备连接到 IoT Central 应用程序时，其设备状态将按如下所述发生更改：

1. 设备状态最初为“已注册”。 此状态表示该设备已在 IoT Central 中创建，并具有设备 ID。 出现以下情况时，设备处于已注册状态：
    - 在“设备”页上添加新的真实设备。
    - 在“设备”页上使用“导入”添加一组设备。

1. 当已使用有效凭据连接到 IoT Central 应用程序的设备完成预配步骤时，设备状态将更改为“已预配”。 在此步骤中，设备使用 DPS 自动从 IoT Central 应用程序所使用的 IoT 中心检索连接字符串。 现在，设备可以连接到 IoT Central 并开始发送数据。

1. 操作员可以阻止设备。 阻止设备时，它无法将数据发送到 IoT Central 应用程序。 已阻止的设备的状态为“已阻止”。 只有在操作员重置设备后，该设备才能继续发送数据。 当操作员取消阻止设备时，状态将恢复为以前的值：“已注册”或“已预配”。

1. 如果设备状态为“正在等待批准”，则表示“自动批准”选项处于禁用状态 。 操作员必须在设备开始发送数据之前显式批准设备。 未在“设备”页上手动注册，但已使用有效凭据建立连接的设备的状态将是“等待批准”。 操作员可以在“设备”页中使用“批准”按钮批准这些设备。

1. 如果设备状态为“未关联”，则表示连接到 IoT Central 的设备没有关联的设备模板。 通常在下列场景中会发生这种情况：

    - 在“设备”页上使用“导入”添加了一组设备，但未指定设备模板。 
    - 在“设备”页上手动注册了设备，但未指定设备模板。 然后使用有效凭据连接设备。  

    操作员可以在“设备”页上使用“迁移”按钮将设备关联到设备模板。

## <a name="device-connection-status"></a>设备连接状态

当设备或边缘设备使用 MQTT 协议进行连接时，生成设备的已连接和已断开连接事件 。 这些事件不是由设备发送的，它们是由 IoT Central 在内部生成的。

下图显示了当设备连接时，如何在一个时间窗口结束时注册连接。 如果发生多个连接和断开连接事件，IoT Central 会注册最接近时间窗口结束的那个事件。 例如，如果设备在时间窗口内断开连接并重新连接，IoT Central 将注册连接事件。 目前，时间窗口大约为一分钟。

:::image type="content" source="media/concepts-get-connected/device-connectivity-diagram.png" alt-text="显示已连接和已断开连接事件的事件时间窗口的示意图。" border="false":::

可以在设备的“原始数据”视图中查看已连接和已断开连接的事件：:::image type="content" source="media/concepts-get-connected/device-connectivity-events.png" alt-text="屏幕截图，显示已筛选的原始数据视图以显示设备连接事件。":::

可以在[从 IoT Central 导出](howto-export-data.md#set-up-data-export)中包含连接和断开连接事件。 若要了解详细信息，请参阅[响应 IoT 中心事件 > 设备已连接和设备已断开连接事件的限制](../../iot-hub/iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events)。

## <a name="sdk-support"></a>SDK 支持

Azure 设备 SDK 为实现设备代码提供最简便的方法。 以下设备 SDK 已发布：

- [适用于 C 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [适用于 Python 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [适用于 Node.js 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [适用于 Java 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [适用于 .NET 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 功能和 IoT 中心连接

与 IoT 中心进行的所有设备通信都使用以下 IoT 中心连接选项：

- [设备到云的消息传送](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [云到设备的消息传递](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [设备孪生](../../iot-hub/iot-hub-devguide-device-twins.md)

下表对 Azure IoT Central 设备功能映射到 IoT 中心功能的具体情况进行了汇总：

| Azure IoT Central | Azure IoT 中心 |
| ----------- | ------- |
| 遥测 | 设备到云的消息传送 |
| 脱机命令 | 云到设备的消息传送 |
| 属性 | 设备孪生报告属性 |
| 属性（可写） | 设备孪生所需的和报告的属性 |
| Command | 直接方法 |

### <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](../../iot-hub/iot-hub-devguide-protocols.md)。

如果设备无法使用这些受支持协议中的任何一种，请使用 Azure IoT Edge 进行协议转换。 IoT Edge 支持其他边缘智能方案，以将处理从 Azure IoT Central 应用程序卸载。

## <a name="security"></a>安全性

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅[控制对 IoT 中心的访问](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>后续步骤

一些建议的后续步骤如下：

- 查看开发设备的[最佳做法](concepts-best-practices.md)。
- 在[教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)中查看一些介绍如何使用 SAS 令牌的示例代码
- 了解[如何使用用于 IoT Central 应用程序的 Node.js 设备 SDK 通过 X.509 证书连接设备](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)
