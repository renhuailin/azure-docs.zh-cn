---
title: 了解 Azure IoT 中心资源的设备更新 |Microsoft Docs
description: 了解 Azure IoT 中心资源的设备更新
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662185"
---
# <a name="device-update-resources"></a>设备更新资源

若要使用 IoT 中心的设备更新，需要创建设备更新帐户和实例资源。 

## <a name="device-update-account"></a>设备更新帐户

设备更新帐户是在 Azure 订阅中创建的资源。 在设备更新帐户级别，可以选择将在其中创建设备更新帐户的区域。 你还可以设置权限来授权将有权访问设备更新的用户。


## <a name="device-update-instance"></a>设备更新实例
创建帐户后，需要创建设备更新实例。 实例是包含与特定 IoT 中心关联的更新和部署的逻辑容器。 设备更新使用 IoT 中心作为设备目录，并使用包含设备的通信通道。 

公共预览期间，可以为每个订阅创建两个设备更新帐户。 此外，还可以为每个帐户创建两个设备更新实例。

## <a name="configuring-device-update-linked-iot-hub"></a>配置设备更新链接 IoT 中心 

为了让设备更新从 IoT 中心接收更改通知，设备更新与 "内置" 事件中心集成。 单击实例中的 "配置 IoT 中心" 按钮将配置所需的消息路由和与 IoT 设备通信所需的访问策略。 

为设备更新配置以下消息路由：

|   路由名称    | 路由查询  | 说明  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |侦听数字克隆更改事件  |
|  DeviceUpdate.DeviceLifeCycle | opType = ' deleteDeviceIdentity '  | 侦听已删除的设备 |
|  DeviceUpdate.TelemetryModelInformation | iothub = "urn： azureiot： ModelDiscovery： ModelInformation：1 | 侦听新设备类型 |
|  DeviceUpdate.DeviceTwinEvents|  (opType = ' updateTwin' OR opType = ' replaceTwin' ) 和 IS_DEFINED ($body. ADUGroup)  | 侦听新设备更新组 |

## <a name="next-steps"></a>后续步骤

[创建设备更新资源](./create-device-update-account.md)
