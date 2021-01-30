---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 028088087b16ded182042aadec4be08a4b8a9589
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062672"
---
# <a name="get-connected-to-azure-iot-central"></a>连接到 Azure IoT Central

*本文适用于操作员和设备开发人员。*

本文介绍设备如何连接到 Azure IoT Central 应用程序。 在设备可以与 IoT Central 交换数据之前，必须执行以下操作：

- *进行身份验证*。 使用 IoT Central 应用程序进行身份验证时，将使用 _共享访问签名 (SAS) 令牌_ 或 _x.509 证书_。 在生产环境中，建议使用 x.509 证书。
- *注册*。 设备必须注册到 IoT Central 应用程序。 可以在应用程序的 " **设备** " 页上查看已注册的设备。
- *与设备模板关联*。 在 IoT Central 应用程序中，设备模板定义操作员用来查看和管理连接设备的 UI。

IoT Central 支持以下两种设备注册方案：

- *自动注册*。 设备首次连接时自动注册。 此方案使 Oem 能够批量制造可以连接的设备，而无需先进行注册。 OEM 生成合适的设备凭据，并在工厂中配置设备。 或者，你可以要求操作员在开始发送数据之前批准设备。 此方案要求在应用程序中配置 x.509 或 SAS _组注册_ 。
- *手动注册*。 操作员可以在 " **设备** " 页面上注册单个设备，或将 [CSV 文件导入](howto-manage-devices.md#import-devices) 到大容量注册设备。 在此方案中，可以使用 x.509 或 SAS _组注册_，或使用 X.509 或 sas _个人注册_。

连接到 IoT Central 的设备应遵循 *IoT 即插即用约定*。 其中一种约定是设备在连接时应发送它所实现的设备模型的 _模型 ID_ 。 模型 ID 使 IoT Central 的应用程序能够将设备与正确的设备模板关联。

IoT Central 使用 [Azure IoT 中心设备预配服务 (DPS) ](../../iot-dps/about-iot-dps.md) 来管理连接过程。 设备首先连接到 DPS 终结点，以检索连接到应用程序所需的信息。 IoT Central 应用程序在内部使用 IoT 中心来处理设备连接。 使用 DPS：

- 可让 IoT Central 支持大规模的设备加入和连接。
- 可让你离线生成设备凭据并配置设备，而无需通过 IoT Central UI 注册设备。
- 可让你使用自己的设备 ID 在 IoT Central 中注册设备。 使用自己的设备 ID 可以简化与现有后端办公系统的集成。
- 可以通过一致的单一方式将设备连接到 IoT Central。

本文介绍下列设备连接步骤：

- [X.509 组注册](#x509-group-enrollment)
- [SAS 组注册](#sas-group-enrollment)
- [单独注册](#individual-enrollment)
- [设备注册](#device-registration)
- [将设备与设备模板关联](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X.509 组注册

在生产环境中，建议使用 X.509 证书作为 IoT Central 的设备身份验证机制。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](../../iot-hub/iot-hub-x509ca-overview.md)。

若要将设备与 x.509 证书连接到应用程序，请执行以下操作：

1. 使用 **(x.509)** 证明类型的证书创建 *注册组*。
1. 添加并验证注册组中的中间或根 x.509 证书。
1. 从注册组中的根或中间证书生成叶证书。 当叶证书连接到应用程序时，从设备发送该证书。

若要了解详细信息，请参阅 [如何用 x.509 证书连接设备](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>仅用于测试目的

仅适用于测试，你可以使用以下实用工具生成根证书、中间证书和设备证书：

- [适用于 Azure IoT 设备预配设备 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)：可用于生成和验证 x.509 证书和密钥的 Node.js 工具的集合。
- 如果使用 DevKit 设备，则此[命令行工具](https://aka.ms/iotcentral-docs-dicetool)会生成一个 CA 证书，可将其添加到 IoT Central 应用程序来验证证书。
- [管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)： PowerShell 和 Bash 脚本的集合：
  - 创建证书链。
  - 将证书保存为 .cer 文件，以上传到 IoT Central 应用程序。
  - 使用 IoT Central 应用程序中的验证码生成验证证书。
  - 在工具中使用设备 ID 作为参数，为设备创建叶证书。

## <a name="sas-group-enrollment"></a>SAS 组注册

将设备与设备 SAS 密钥连接到应用程序：

1. 创建使用 **共享访问签名 (SAS)** 证明类型的 *注册组*。
1. 从注册组中复制组主密钥或辅助密钥。
1. 使用 Azure CLI 从组密钥生成设备密钥：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 当设备连接到 IoT Central 应用程序时，使用生成的设备密钥。

## <a name="individual-enrollment"></a>单独注册

连接设备的每个客户都有自己的身份验证凭据，使用单独的注册。 单个注册是允许连接的单个设备的条目。 个人注册可使用 x.509 叶证书或 SAS 令牌 (从物理或虚拟受信任的平台模块) 作为证明机制。 设备 ID 中可以包含字母、数字和 `-` 字符。 有关详细信息，请参阅 [DPS 单个注册](../../iot-dps/concepts-service.md#individual-enrollment)。

> [!NOTE]
> 为设备创建单个注册时，其优先级高于 IoT Central 应用程序中的默认组注册选项。

### <a name="create-individual-enrollments"></a>创建单个注册

IoT Central 支持用于单个注册的以下证明机制：

- **对称密钥证明：** 对称密钥证明是使用 DPS 实例对设备进行身份验证的一种简单方法。 若要创建使用对称密钥的单个注册，请打开设备的 " **设备连接** " 页，选择 " **单个注册** " 作为 "连接方法"，并将 " **共享访问签名" (SAS)** 作为机制。 输入 base64 编码的主密钥和辅助密钥，并保存所做的更改。 使用 **id 范围**、 **设备 ID** 以及主密钥或辅助密钥来连接设备。

    > [!TIP]
    > 对于测试，可以使用 **OpenSSL** 生成 base64 编码的密钥： `openssl rand -base64 64`

- **X.509 证书：** 若要使用 x.509 证书创建单个注册，请打开 " **设备连接** " 页，选择 " **单个注册** " 作为连接方法，并将 **证书 (x.509)** 为该机制。 与单个注册条目一起使用的设备证书要求将颁发者和使用者 CN 设置为设备 ID。

    > [!TIP]
    > 对于测试，可以使用适用于 [Node.js的 Azure IoT 设备预配设备 SDK 工具 ](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) 来生成自签名证书： `node create_test_cert.js device "mytestdevice"`

- **受信任的平台模块 (TPM) 证明：**[TPM](../../iot-dps/concepts-tpm-attestation.md)是一种硬件安全模块。 使用 TPM 是连接设备的最安全方式之一。 本文假设使用的是独立的、固件或集成的 TPM。 软件仿真 Tpm 非常适合用于原型制作或测试，但它们不提供与离散、固件或集成 Tpm 相同的安全性级别。 请勿在生产环境中使用软件 Tpm。 若要创建使用 TPM 的单个注册，请打开 " **设备连接** " 页，选择 " **单个注册** " 作为 "连接方法"，选择 " **TPM** " 作为机制。 输入 TPM 认可密钥并保存设备连接信息。

## <a name="device-registration"></a>设备注册

必须先在应用程序中注册设备，然后才能连接到 IoT Central 应用程序：

- 设备首次连接时可自动注册。 若要使用此选项，必须使用 [x.509 组注册](#x509-group-enrollment) 或 [SAS 组注册](#sas-group-enrollment)。
- 操作员可以导入 CSV 文件以批量注册应用程序中的设备列表。
- 操作员可以在应用程序的 " **设备** " 页上手动注册单个设备。

IoT Central 使 Oem 能够批量制造可自动注册的设备。 OEM 生成合适的设备凭据，并在工厂中配置设备。 当客户首次打开设备时，它将连接到 DPS，然后将设备自动连接到正确的 IoT Central 应用程序。 或者，你可以在开始向应用程序发送数据之前要求操作员批准设备。

> [!TIP]
> 在 " **管理 > 设备连接** " 页上，" **自动批准** " 选项控制操作员是否必须手动批准设备，然后才能开始发送数据。

### <a name="automatically-register-devices-that-use-x509-certificates"></a>自动注册使用 x.509 证书的设备

1. 使用添加到 [x.509 注册组](#x509-group-enrollment)的根或中间证书，为设备生成叶证书。 使用设备 Id 作为 `CNAME` 叶证书中的。 设备 ID 中可以包含字母、数字和 `-` 字符。

1. 作为 OEM，使用设备 ID、生成的 x.509 叶证书和应用程序 **ID 范围** 值闪烁每台设备。 设备代码还应发送它所实现的设备模型的模型 ID。

1. 当你在设备上切换时，它将首先连接到 DPS 以检索其 IoT Central 连接信息。

1. 设备使用来自 DPS 的信息连接到 IoT Central 应用程序并向其注册。

IoT Central 应用程序使用设备发送的模型 ID [将注册的设备与设备模板关联](#associate-a-device-with-a-device-template)。

### <a name="automatically-register-devices-that-use-sas-tokens"></a>自动注册使用 SAS 令牌的设备

1. 从 " **SAS-设备** " 注册组中复制组主密钥：

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="将 SAS-设备注册组中的主密钥分组":::

1. 使用 `az iot central device compute-device-key` 命令生成设备 SAS 密钥。 使用上一步中的 "组主密钥"。 设备 ID 可以包含字母、数字和 `-` 字符：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 作为 OEM，使用设备 ID、生成的设备 SAS 密钥和应用程序 **ID 范围** 值闪烁每台设备。 设备代码还应发送它所实现的设备模型的模型 ID。

1. 打开设备时，它首先会连接到 DPS 以检索其 IoT Central 注册信息。

1. 设备使用来自 DPS 的信息连接到 IoT Central 应用程序并向其注册。

IoT Central 应用程序使用设备发送的模型 ID [将注册的设备与设备模板关联](#associate-a-device-with-a-device-template)。

### <a name="bulk-register-devices-in-advance"></a>提前批量注册设备

若要将大量设备注册到 IoT Central 应用程序，请使用 CSV 文件[导入设备 ID 和设备名称](howto-manage-devices.md#import-devices)。

如果设备使用 SAS 令牌进行身份验证，请 [从 IoT Central 应用程序导出 CSV 文件](howto-manage-devices.md#export-devices)。 导出的 CSV 文件包括设备 Id 和 SAS 密钥。

如果设备使用 x.509 证书进行身份验证，请使用上载到 x.509 注册组的根或中间证书，为设备生成 x.509 叶证书。 使用导入的设备 Id 作为 `CNAME` 叶证书中的值。

设备必须使用应用程序的 " **ID 范围** " 值，并在连接时发送模型 id。

> [!TIP]
> 你可以在 "**管理" > 设备连接**"中找到" **ID 范围**"值。

### <a name="register-a-single-device-in-advance"></a>提前注册单个设备

在体验 IoT Central 或测试设备时，此方法非常有用。 在 "**设备**" 页上选择 " **+ 新建**" 以注册单个设备。 可以使用设备连接 SAS 密钥将设备连接到 IoT Central 应用程序。 从已注册设备的连接信息中复制 _设备 SAS 密钥_ ：

![单个设备的 SAS 密钥](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>将设备与设备模板关联

当设备连接时，IoT Central 会自动将设备与设备模板关联。 设备连接时，它会发送 [模型 ID](../../iot-pnp/iot-plug-and-play-glossary.md#model-id) 。 IoT Central 使用模型 ID 标识特定设备型号的设备模板。 发现过程的工作方式如下：

1. 如果设备模板已在 IoT Central 应用程序中发布，则设备将与设备模板关联。
1. 如果未在 IoT Central 应用程序中发布设备模板，IoT Central 会在 [公共模型存储库](https://github.com/Azure/iot-plugandplay-models)中查找设备型号。 如果 IoT Central 找到模型，它将使用它来生成基本设备模板。
1. 如果 IoT Central 在公共模型存储库中找不到该模型，则该设备将被标记为未 **关联**。 操作员可以为设备创建设备模板，然后将未关联的设备迁移到新设备模板。

以下屏幕截图演示了如何在 IoT Central 中查看设备模板的模型 ID。 在设备模板中，选择一个组件，然后选择 " **查看标识**：

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="显示恒温器设备模板中的模型 ID 的屏幕截图。":::

您可以在公共模型存储库中查看 [恒温器模型](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) 。 模型 ID 定义如下所示：

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>设备状态值

当真实设备连接到 IoT Central 应用程序时，其设备状态将按如下所述发生更改：

1. 设备状态最初为“已注册”。 此状态表示该设备已在 IoT Central 中创建，并具有设备 ID。 出现以下情况时，设备处于已注册状态：
    - 在“设备”页上添加新的真实设备。
    - 在“设备”页上使用“导入”添加一组设备。

1. 当已使用有效凭据连接到 IoT Central 应用程序的设备完成预配步骤时，设备状态将更改为“已预配”。 在此步骤中，设备使用 DPS 自动从 IoT Central 应用程序所使用的 IoT 中心检索连接字符串。 设备现在可以连接到 IoT Central 并开始发送数据。

1. 操作员可以阻止设备。 阻止设备时，它无法将数据发送到 IoT Central 应用程序。 已阻止的设备的状态为“已阻止”。 只有在操作员重置设备后，该设备才能继续发送数据。 当操作员取消阻止设备时，状态将恢复为以前的值：“已注册”或“已预配”。

1. 如果设备状态为 " **正在等待批准**"，则表示 " **自动批准** " 选项处于禁用状态。 操作员必须先显式批准设备，然后才能开始发送数据。 未在“设备”页上手动注册，但已使用有效凭据建立连接的设备的状态将是“等待批准”。 操作员可以在“设备”页中使用“批准”按钮批准这些设备。

1. 如果设备状态为 "未 **关联**"，则表示连接到 IoT Central 的设备没有关联的设备模板。 这种情况通常发生在以下情况下：

    - 使用 " **导入** " 在 " **设备** " 页上添加一组设备，而无需指定设备模板。
    - 设备已在 " **设备** " 页上手动注册，无需指定设备模板。 然后，设备会连接到有效的凭据。  

    操作员可以使用 "**迁移**" 按钮，将设备从 "**设备**" 页关联到设备模板。

## <a name="best-practices"></a>最佳做法

第一次连接设备时，请勿保留或缓存 DPS 返回的设备连接字符串。 若要重新连接设备，请执行标准设备注册流以获取正确的设备连接字符串。 如果设备缓存连接字符串，则设备软件面临具有陈旧连接字符串的风险。 如果 IoT Central 更新其使用的基础 Azure IoT 中心，则具有陈旧连接字符串的设备将无法连接。

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
| 遥测技术 | 设备到云的消息传送 |
| 脱机命令 | 云到设备的消息传送 |
| properties | 设备孪生报告属性 |
| 属性（可写） | 设备孪生所需的和报告的属性 |
| Command | 直接方法 |

### <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](../../iot-hub/iot-hub-devguide-protocols.md)。

如果设备无法使用任何受支持的协议，请使用 Azure IoT Edge 执行协议转换。 IoT Edge 支持其他用于从 Azure IoT Central 应用程序中卸载处理的智能边界方案。

## <a name="security"></a>安全性

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅 [控制对 IoT 中心的访问](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员，则建议执行以下后续步骤：

- 查看一些演示如何使用 SAS 令牌的示例代码，请参阅 [教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)
- 了解如何 [使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解如何 [在 Azure IoT Central 应用程序中定义新的 IoT 设备类型](./howto-set-up-template.md)
- 了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)
