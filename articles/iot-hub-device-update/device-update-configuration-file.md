---
title: 了解 Azure IoT 中心配置文件的设备更新 |Microsoft Docs
description: 了解 Azure IoT 中心配置文件的设备更新。
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661863"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>IoT 中心配置文件的设备更新

"adu-conf.txt" 是可选文件，可以创建该文件来管理以下配置。

* AzureDeviceUpdateCore： ClientMetadata： deviceProperties ["manufacturer"]
* AzureDeviceUpdateCore： ClientMetadata： deviceProperties ["model"]
* DeviceInformation 制造商
* DeviceInformation
* 如果设备更新代理) 不知道设备连接字符串 (，则为。

## <a name="purpose"></a>目的
设备更新代理将首先尝试 `manufacturer` `model` 从设备获取和值，以用于 [接口层](device-update-agent-overview.md#the-interface-layer)。 如果此操作失败，则设备更新代理将随后查找 "adu-conf.txt" 文件并使用其中的值。 如果两次尝试都不成功，则设备更新代理将使用 [默认](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) 值。

了解有关 [ADU 核心接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) 和 [设备信息接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)的详细信息。

## <a name="file-location"></a>文件位置

在 Linux 系统中，在名为的分区或磁盘中 `adu` ，创建一个名为 "adu-conf.txt" 的文本文件，其中包含以下字段。

## <a name="list-of-fields"></a>字段列表

|名称|说明|
|-----------|--------------------|
|connection_string|设备可用于连接到 IoT 中心的预设置连接字符串。 注意：如果通过[Azure IoT 标识服务](https://azure.github.io/iot-identity-service/)预配设备更新代理，则不需要|
|aduc_manufacturer|由接口报告 `AzureDeviceUpdateCore:4.ClientMetadata:4` ，用于对设备进行分类以实现更新部署。|
|aduc_model|由接口报告 `AzureDeviceUpdateCore:4.ClientMetadata:4` ，用于对设备进行分类以实现更新部署。|
|制造商|作为接口的一部分由设备更新代理报告 `DeviceInformation` 。|
|模型|作为接口的一部分由设备更新代理报告 `DeviceInformation` 。|

## <a name="example-adu-conftxt-file-contents"></a>示例 "adu-conf.txt" 文件内容

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
