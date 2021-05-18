---
title: 了解 Device Update for Azure IoT Hub 配置文件 | Microsoft Docs
description: 了解 Device Update for Azure IoT Hub 配置文件。
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661863"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Device Update for IoT Hub 配置文件

“adu-conf.txt”是一个可选文件，可创建它来管理以下配置。

* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]
* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]
* DeviceInformation.manufacturer
* DeviceInformation.model
* 设备连接字符串（如果 Device Update 代理不知道该字符串）。

## <a name="purpose"></a>目标
Device Update 代理将首先尝试从设备获取 `manufacturer` 和 `model` 值来用于[接口层](device-update-agent-overview.md#the-interface-layer)。 如果此操作失败，Device Update 代理接下来将查找“adu-conf.txt”文件并使用其中的值。 如果上述两种尝试都失败，Device Update 代理将使用[默认](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt)值。

了解有关 [ADU 核心接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface)和[设备信息接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)的详细信息。

## <a name="file-location"></a>文件位置

在 Linux 系统中，在名为 `adu` 的分区或磁盘中，创建一个包含以下字段的名为“adu-conf.txt”的文本文件。

## <a name="list-of-fields"></a>字段列表

|名称|说明|
|-----------|--------------------|
|connection_string|设备可用于连接到 IoT 中心的预设置的连接字符串。 注意：如果通过 [Azure IoT 标识服务](https://azure.github.io/iot-identity-service/)预配 Device Update 代理，则此字段不是必需字段|
|aduc_manufacturer|由 `AzureDeviceUpdateCore:4.ClientMetadata:4` 接口报告，用于对设备进行分类来确定更新部署的目标。|
|aduc_model|由 `AzureDeviceUpdateCore:4.ClientMetadata:4` 接口报告，用于对设备进行分类来确定更新部署的目标。|
|制造商|由 Device Update 代理报告为 `DeviceInformation` 接口的一部分。|
|模型|由 Device Update 代理报告为 `DeviceInformation` 接口的一部分。|

## <a name="example-adu-conftxt-file-contents"></a>示例“adu-conf.txt”文件内容

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
