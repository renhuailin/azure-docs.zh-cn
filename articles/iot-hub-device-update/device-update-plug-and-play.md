---
title: 了解 IoT 中心的设备更新如何使用 IoT 即插即用 | Microsoft Docs
description: IoT 中心的设备更新用于发现和管理支持无线更新的设备。
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e53aa866465e7a1409a966a5bab6147d304caddf
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605328"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT 中心的设备更新和 IoT 即插即用

IoT 中心的设备更新使用 [IoT 即插即用](../iot-develop/index.yml)以发现和管理支持无线更新的设备。 设备更新服务将使用 IoT 即插即用接口接收来自设备的属性和消息，以及向设备发送属性和消息。 IoT 中心的设备更新要求 IoT 设备实现以下接口和模型-ID（如下所述）。

概念： 
* 了解 [IoT 即插即用设备客户端](../iot-develop/concepts-developer-guide-device.md?pivots=programming-language-csharp)。 
* 查看如何[实现设备更新代理](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)。

## <a name="adu-core-interface"></a>ADU Core 接口

“ADUCoreInterface”接口用于向设备发送更新操作和元数据，并从设备接收更新状态。 “ADU Core”接口拆分为两个对象属性。

实现此接口时，模型中的预期组件名称为“azureDeviceUpdateAgent”。 [详细了解 Azure IoT 即插即用组件](../iot-develop/concepts-modeling-guide.md)

### <a name="agent-metadata"></a>代理元数据

设备或设备更新代理会使用代理元数据中包含的字段向设备更新服务发送信息和状态。

|名称|架构|方向|描述|示例|
|----|------|---------|-----------|-----------|
|resultCode|integer|设备到云|此代码包含上次更新操作结果的信息。 可能填充成功，也可能填充失败，应遵循 [HTTP 状态代码规范](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。|500|
|extendedResultCode|integer|设备到云|此代码包含结果的其他信息。 可能填充成功，也可能填充失败。|0x80004005|
|state|integer|设备到云|此整数用于指示设备更新代理的当前状态。 详细信息参见以下内容 |闲置|
|installedUpdateId|字符串|设备到云|当前（通过设备更新）安装的更新 ID。 此值将是一个字符串，用于捕获更新 ID JSON，对于从未通过设备更新进行更新的设备，此值将为 NULL。|"{\"provider\":\"contoso\",\"name\":\"image-update\",\"version\":\"1.0.0\"}"|
|`deviceProperties`|映射|设备到云|包含制造商和型号的属性集。|详细信息参见以下内容

#### <a name="state"></a>状态

此为设备更新代理从设备更新服务接收操作后报告的状态。 系统会报告 `State`，以响应从设备更新服务发送到设备更新代理的 `Action`（请参阅下文 `Actions`）。 请参阅[概述工作流](understand-device-update.md#device-update-agent)，以了解在设备更新服务和设备更新代理之间流动的请求。

|名称|值|说明|
|---------|-----|-----------|
|闲置|0|设备已准备就绪，可以接收来自设备更新服务的操作。 更新成功后，状态返回到 `Idle`。|
|DownloadSucceeded|2|下载成功。|
|InstallSucceeded|4|安装成功。|
|失败|255|更新时出错。|

#### <a name="device-properties"></a>设备属性

此为包含制造商和型号的属性集。

|名称|架构|方向|描述|
|----|------|---------|-----------|
|制造商|字符串|设备到云|设备的制造商，通过 `deviceProperties` 报告。 可以从两个位置的任一位置读取此属性：“AzureDeviceUpdateCore”接口将首先尝试从[配置文件](device-update-configuration-file.md)中读取“aduc_manufacturer”值。  如果未在配置文件中填充该值，则默认情况下会报告 ADUC_DEVICEPROPERTIES_MANUFACTURER 的编译时定义。 仅在启动时报告此属性。 默认值“Contoso”|
|模型|字符串|设备到云|设备的型号，通过 `deviceProperties` 报告。 可以从两个位置的任一位置读取此属性：AzureDeviceUpdateCore 接口将首先尝试从[配置文件](device-update-configuration-file.md)中读取“aduc_model”值。  如果未在配置文件中填充该值，则默认情况下会报告 ADUC_DEVICEPROPERTIES_MODEL 的编译时定义。 仅在启动时报告此属性。 默认值“Video”|
|aduVer|字符串|设备到云|设备上运行的设备更新代理的版本。 仅在编译时 ENABLE_ADU_TELEMETRY_REPORTING 设置为 1 (true) 的情况下，才从生成中读取该值。 客户可以通过将该值设置为 0 (false) 选择退出版本报告。 [如何自定义设备更新代理属性](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)。|
|doVer|字符串|设备到云|设备上运行的传递优化代理的版本。 仅在编译时 ENABLE_ADU_TELEMETRY_REPORTING 设置为 1 (true) 的情况下，才从生成中读取该值。 客户可以通过将该值设置为 0 (false) 选择退出版本报告。[如何自定义传递优化代理属性](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)。|

IoT 中心设备孪生示例
```json
 "azureDeviceUpdateAgent": {
                           "__t": "c",
                           "client": {
                                     "state": 0,
                                     "resultCode": 200,
                                     "extendedResultCode": 0,
                                     "deviceProperties": {
                                                         "manufacturer": "Contoso",
                                                         "model": "Video",
                                                         "aduVer": "DU;agent/0.6.0",
                                                         "doVer": "DU;lib/v0.4.0,DU;agent/v0.4.0,DU;plugin-apt/v0.2.0"
                                                         },
                                     "installedUpdateId": "{\"provider\":\"Contoso\",\"name\":\"SampleUpdate1\",\"version\":\"1.0.4\"}"
                                     },
                            }
```

注意：设备或模块必须添加 {"__t": "c"} 标记以指示元素引用组件，请在[此处](../iot-develop/concepts-convention.md#sample-multiple-components-writable-property)了解详细信息。

### <a name="service-metadata"></a>服务元数据

设备更新服务会使用服务元数据中包含的字段向设备更新代理传达操作和数据。

|名称|架构|方向|描述|
|----|------|---------|-----------|
|action|integer|云到设备|此整数与代理应执行的操作相对应。 其值如下所列。|
|updateManifest|字符串|云到设备|用于描述更新的内容。 根据[导入清单](import-update.md#create-a-device-update-import-manifest)生成|
|updateManifestSignature|JSON 对象|云到设备|JSON Web 签名 (JWS)，具有用于源验证的 JSON Web 密钥。|
|fileUrls|映射|云到设备|`FileHash` 到 `DownloadUri` 的映射。 告知代理要下载的文件和用于验证文件是否已正确下载的哈希。|

#### <a name="action"></a>操作

如下 `Actions` 表示设备更新代理按照设备更新服务的指示所执行的操作。 设备更新代理将报告处理收到的 `Action` 的 `State`（请参阅上述 `State` 部分）。 请参阅[概述工作流](understand-device-update.md#device-update-agent)，以了解在设备更新服务和设备更新代理之间流动的请求。

|名称|值|说明|
|---------|-----|-----------|
|下载|0|下载已发布的内容或更新以及所需的任何其他内容|
|安装|1|安装内容或更新。 通常，这意味着调用内容或更新的安装程序。|
|应用|2|完成更新。 必要时，它会指示系统重新启动。|
|取消|255|停止处理当前操作，并返回到 `Idle`。 也用于告知处于 `Failed` 状态的代理返回到 `Idle`。|

## <a name="device-information-interface"></a>设备信息接口

设备信息接口是 [IoT 即插即用体系结构](../iot-develop/overview-iot-plug-and-play.md)中使用的概念。 它包含设备到云的属性，这些属性提供有关设备的硬件和操作系统的信息。 IoT 中心的设备更新使用 DeviceInformation.manufacturer 和 DeviceInformation.model 属性进行遥测和诊断。 若要详细了解设备信息接口，请参阅此[示例](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)。

实现此接口时，模型中的预期组件名称为 deviceInformation。 [了解 Azure IoT 即插即用组件](../iot-develop/concepts-modeling-guide.md)

|名称|类型|架构|方向|描述|示例|
|----|----|------|---------|-----------|-----------|
|制造商|属性|字符串|设备到云|设备制造商的公司名称。 此名称可以与原始设备制造商 (OEM) 的名称相同。|Contoso|
|模型|属性|字符串|设备到云|设备型号名称或 ID。|IoT Edge 设备|
|swVersion|属性|字符串|设备到云|设备上的软件版本。 swVersion 可能是固件版本。|4.15.0-122|
|osName|属性|字符串|设备到云|设备上的操作系统名称。|Ubuntu Server 18.04|
|processorArchitecture|属性|字符串|设备到云|设备上处理器的体系结构。|ARM64|
|processorManufacturer|属性|字符串|设备到云|设备上处理器制造商的名称。|Microsoft|
|totalStorage|属性|字符串|设备到云|设备上的可用存储总量（以千字节为单位）。|2048|
|totalMemory|属性|字符串|设备到云|设备上的可用内存总量（以千字节为单位）。|256|

## <a name="model-id"></a>模型 ID 

模型 ID 是智能设备通过 IoT 即插即用向 Azure IoT 应用程序发布其功能的方式。若要了解如何构建智能设备以向 Azure IoT 应用程序发布其功能的详细信息，请访问《[IoT 即插即用设备开发人员指南](../iot-develop/concepts-developer-guide-device.md)》。

IoT 中心的设备更新要求 IoT 即插即用智能设备在设备连接过程中公布值为“dtmi:AzureDeviceUpdate;1” 的模型 ID。 [了解如何公布模型 ID](../iot-develop/concepts-developer-guide-device.md#model-id-announcement)。