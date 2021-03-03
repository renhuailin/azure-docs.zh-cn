---
title: 了解 IoT 中心的设备更新如何使用 IoT 即插即用 |Microsoft Docs
description: IoT 中心的设备更新用于发现和管理支持无线更新的设备。
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 227488f165aaad2f204c647eed17467a4ef561a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662135"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT 中心和 IoT 即插即用的设备更新

IoT 中心的设备更新使用 [iot 即插即用](https://docs.microsoft.com/azure/iot-pnp/) 发现和管理支持无线更新功能的设备。 设备更新服务将使用 PnP 接口在设备之间发送和接收属性和消息。 IoT 中心的设备更新要求 IoT 设备实现以下接口和型号 id （如下所述）。

## <a name="adu-core-interface"></a>ADU 核心接口

"ADUCoreInterface" 接口用于将更新操作和元数据发送到设备，并从设备接收更新状态。 "ADU Core" 接口拆分为两个对象属性。

实现此接口时，模型中的预期组件名称为 **"azureDeviceUpdateAgent"** 。 [详细了解 Azure IoT PnP 组件](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

### <a name="agent-metadata"></a>代理元数据

代理元数据包含设备或设备更新代理用于将信息和状态发送到设备更新服务的字段。

|名称|架构|方向|说明|示例|
|----|------|---------|-----------|-----------|
|resultCode|integer|设备到云|一个代码，包含有关上一次更新操作结果的信息。 可以填充成功或失败，并且应遵循 [http 状态代码规范](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。|500|
|extendedResultCode|integer|设备到云|包含有关结果的其他信息的代码。 可以填充成功或失败。|0x80004005|
|state|integer|设备到云|它是一个整数，用于指示设备更新代理的当前状态。 详细信息参见以下内容 |闲置|
|installedUpdateId|字符串|设备到云|当前通过设备更新)  (安装的更新的 ID。 对于从未通过设备更新进行更新的设备，此值将为 null。|Null|
|`deviceProperties`|映射|设备到云|包含制造商和型号的属性集。|详细信息参见以下内容

#### <a name="state"></a>状态

它是从设备更新服务接收操作后设备更新代理报告的状态。 `State` 为响应 (如下所示 `Action` `Actions`) 从设备更新服务发送到设备更新代理。 请参阅 [概述工作流](understand-device-update.md#device-update-agent) ，了解在设备更新服务和设备更新代理之间流动的请求。

|名称|“值”|说明|
|---------|-----|-----------|
|闲置|0|设备已准备好接收来自设备更新服务的操作。 成功更新后，状态将返回到 `Idle` 状态。|
|DownloadSucceeded|2|下载成功。|
|InstallSucceeded|4|安装成功。|
|已失败|255|更新时出错。|

#### <a name="device-properties"></a>设备属性

它是包含制造商和型号的属性集。

|名称|架构|方向|说明|
|----|------|---------|-----------|
|制造商|字符串|设备到云|设备的设备制造商，通过报告 `deviceProperties` 。 此属性从两个位置中的一个位置读取-"AzureDeviceUpdateCore" 接口将首先尝试从 [配置文件](device-update-configuration-file.md) 文件中读取 "aduc_manufacturer" 值。  如果未在配置文件中填充该值，则默认情况下会报告 ADUC_DEVICEPROPERTIES_MANUFACTURER 的编译时定义。 此属性仅在启动时报告。|
|模型|字符串|设备到云|设备的设备型号，通过报告 `deviceProperties` 。 此属性是从两个位置中的一个位置读取的，AzureDeviceUpdateCore 接口将首先尝试从 [配置文件](device-update-configuration-file.md) 文件中读取 "aduc_model" 值。  如果未在配置文件中填充该值，则默认情况下会报告 ADUC_DEVICEPROPERTIES_MODEL 的编译时定义。 此属性仅在启动时报告。|
|aduVer|字符串|设备到云|设备上运行的设备更新代理的版本。 仅在编译时将此值从生成中读取 ENABLE_ADU_TELEMETRY_REPORTING 设置为 1 (true) 。 客户可以通过将值设置为 0 (false) 选择退出版本报告。 [如何自定义设备更新代理属性](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)。|
|doVer|字符串|设备到云|在设备上运行的传递优化代理的版本。 仅当编译时 ENABLE_ADU_TELEMETRY_REPORTING 设置为 1 (true) 时，才从生成中读取该值。 客户可以通过将值设置为 0 (false) 来选择退出版本报告。[如何自定义传递优化代理属性](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)。|

### <a name="service-metadata"></a>服务元数据

服务元数据包含设备更新服务用于向设备更新代理传达操作和数据的字段。

|名称|架构|方向|说明|
|----|------|---------|-----------|
|action|integer|云到设备|它是对应于代理应执行的操作的整数。 下面列出的值。|
|updateManifest|字符串|云到设备|用于描述更新的内容。 从[导入清单](import-update.md#create-device-update-import-manifest)生成|
|updateManifestSignature|JSON 对象|云到设备|使用用于源验证的 JSON Web 密钥 (JWS) 的 JSON Web 签名。|
|Fileurl|映射|云到设备|到的 `FileHash` 映射 `DownloadUri` 。 告知代理、要下载的文件和用于验证文件是否已正确下载的哈希。|

#### <a name="action"></a>操作

`Actions` 下面的表示设备更新代理执行的操作，如设备更新服务所述。 设备更新代理将报告 `State` (参阅 `State` 上述部分) 处理 `Action` 收到的。 请参阅 [概述工作流](understand-device-update.md#device-update-agent) ，了解在设备更新服务和设备更新代理之间流动的请求。

|名称|“值”|说明|
|---------|-----|-----------|
|下载|0|下载已发布内容或更新和所需的任何其他内容|
|安装|1|安装内容或更新。 通常，这意味着调用内容或更新的安装程序。|
|应用|2|完成更新。 必要时，它会指示系统重新启动。|
|取消|255|停止处理当前操作并返回到 `Idle` 。 还将用于告知代理 `Failed` 状态返回到 `Idle` 。|

## <a name="device-information-interface"></a>设备信息接口

设备信息接口是 [IoT 即插即用体系结构](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)中使用的概念。 它包含设备到云的属性，这些属性提供有关设备的硬件和操作系统的信息。 IoT 中心的设备更新使用 DeviceInformation 和 DeviceInformation 属性进行遥测和诊断。 若要了解有关设备信息接口的详细信息，请参阅此 [示例](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)。

实现此接口时，模型中的预期组件名称是 **deviceInformation** 。 [了解 Azure IoT PnP 组件](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

|名称|类型|架构|方向|说明|示例|
|----|----|------|---------|-----------|-----------|
|制造商|属性|字符串|设备到云|设备制造商的公司名称。 此名称可以与原始设备制造商 (OEM) 的名称相同。|Contoso|
|模型|属性|字符串|设备到云|设备型号名称或 ID。|IoT Edge 设备|
|swVersion|属性|字符串|设备到云|设备上的软件版本。 swVersion 可能是固件的版本。|4.15.0-122|
|osName|属性|字符串|设备到云|设备上的操作系统的名称。|Ubuntu Server 18.04|
|processorArchitecture|属性|字符串|设备到云|设备上的处理器的体系结构。|ARM64|
|processorManufacturer|属性|字符串|设备到云|设备上的处理器制造商的名称。|Microsoft|
|totalStorage|属性|字符串|设备到云|设备上的可用存储总量（kb）。|2048|
|totalMemory|属性|字符串|设备到云|设备上的可用内存总量（kb）。|256|

## <a name="model-id"></a>模型 ID 

模型 ID 是指智能设备如何通过 IoT 插件向 Azure IoT 应用程序公布其功能。有关详细信息，请参阅如何构建智能设备以便将其功能播发到 Azure IoT 应用程序 [即插即用设备开发人员指南](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c)。

IoT 中心的设备更新要求 IoT 即插即用智能设备在设备连接过程中公布值为 **"dtmi： AzureDeviceUpdate; 1"** 的模型 ID。 [了解如何公布模型 ID](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c#model-id-announcement)。
