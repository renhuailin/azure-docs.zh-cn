---
title: 了解 Device Update for Azure IoT Hub 资源 | Microsoft Docs
description: 了解 Device Update for Azure IoT Hub 资源
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e8194a269aec09f087632d2f6069d0624d7a835b
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111410650"
---
# <a name="device-update-resources"></a>设备更新资源

若要使用 Device Update for IoT Hub，需要创建设备更新帐户和实例资源。 

## <a name="device-update-account"></a>设备更新帐户

设备更新帐户是在 Azure 订阅中创建的资源。 在设备更新帐户级别，可以选择将在其中创建设备更新帐户的区域。 还可以设置权限，对将有权访问设备更新的用户进行授权。


## <a name="device-update-instance"></a>设备更新实例
创建帐户后，需要创建设备更新实例。 实例是包含与特定 IoT 中心关联的更新和部署的逻辑容器。 设备更新使用 IoT 中心作为设备目录，并使用包含设备的通信通道。 

在公共预览版中，可以为每个订阅创建两个设备更新帐户。 此外，还可以为每个帐户创建两个设备更新实例。

## <a name="configuring-device-update-linked-iot-hub"></a>配置设备更新链接的 IoT 中心 

为了让设备更新从 IoT 中心收到更改通知，设备更新与“内置”的事件中心集成。 单击实例内的“配置 IoT 中心”按钮可配置所需消息路由、使用者组以及与 IoT 设备通信所需的访问策略。 

### <a name="message-routing"></a>消息路由

为设备更新配置以下消息路由：

|   路由名称    | 数据源 | 路由查询  | 终结点 | 说明  |
| :--------- | :---- |:---- |:---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | DigitalTwinChangeEvents | 是 | 活动 | 侦听数字孪生体更改事件  |
|  DeviceUpdate.DeviceLifecycle | DeviceLifecycleEvents | opType = 'deleteDeviceIdentity' 或 opType = 'deleteModuleIdentity'  | 活动 | 侦听已删除的设备 |
|  DeviceUpdate.DeviceTwinEvents| TwinChangeEvents | (opType = 'updateTwin' OR opType = 'replaceTwin') AND IS_DEFINED($body.tags.ADUGroup) | 活动 | 侦听新设备更新组 |

> [!NOTE]
> 配置这些路由时，路由名称并不重要。 我们将 DeviceUpdate 作为前缀，以使名称一致，并易于识别它们是用于设备更新。 其余的路由属性应完全按照下表进行配置，以便设备更新正常工作。 

### <a name="consumer-group"></a>使用者组

配置 IoT 中心还会创建设备更新管理服务所需的事件中心使用者组。 

:::image type="content" source="media/device-update-resources/consumer-group.png" alt-text="使用者组的屏幕截图。" lightbox="media/device-update-resources/consumer-group.png":::

### <a name="access-policy"></a>访问策略

设备更新管理服务需要名为“deviceupdateservice”的共享访问策略来查询支持更新的设备。 “deviceupdateservice”策略是在配置 IoT 中心时创建的，并具有以下权限：
- 注册表读取
- 服务连接
- 设备连接

:::image type="content" source="media/device-update-resources/access-policy.png" alt-text="访问策略的屏幕截图。" lightbox="media/device-update-resources/access-policy.png":::

## <a name="next-steps"></a>后续步骤

[创建设备更新资源](./create-device-update-account.md)
