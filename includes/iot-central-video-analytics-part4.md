---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b3ccc31c9159b5d7b1615add89e8fdc308bf8df
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763387"
---
### <a name="publish-the-device-template"></a>发布设备模板

在将设备添加到应用程序之前，必须先发布设备模板：

1. 在“LVA Edge 网关 v2”设备模板中选择“发布” 。

1. 在“将此设备模板发布到应用程序”页中，选择“发布” 。

LVA Edge 网关 v2 现在可作为设备类型在应用程序的“设备”页上使用 。

## <a name="migrate-the-gateway-device"></a>迁移网关设备

现有的 gateway-001 设备使用 LVA Edge 网关设备模板 。 若要使用新的部署清单，请将设备迁移到新的设备模板：

迁移 gateway-001 设备：

1. 导航到“设备”页，然后选择“gateway-001”设备以在列表中突出显示该设备 。

1. 选择“迁移”。 如果“迁移”图标不可见，请选择“…”以查看更多选项 。

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="将网关设备迁移到新版本":::

1. 在“迁移”对话框的列表中，选择“LVA Edge 网关 v2”，然后选择“迁移”  。

几秒钟后，迁移完成。 你的设备现在使用带有自定义的部署清单的 LVA Edge 网关 v2 设备模板。

目前没有设备使用原始的“LVA Edge 网关”设备模板。 删除此设备模板：

1. 导航到“设备模板”页，然后选择“LVA Edge 网关”设备模板 。

1. 选择“删除”以删除设备模板。

### <a name="get-the-device-credentials"></a>获取设备凭据

要使设备能够连接到 IoT Central 应用程序，则需要提供凭据。 若要获取设备凭据：

1. 在“设备”页上，选择 gateway-001 设备 。

1. 选择“连接”。

1. 在“设备连接”页上，在 scratchpad.txt 文件中记下 ID 范围、设备 ID 和设备主密钥  。 稍后要使用这些值。

1. 请确保将连接方法设置为“共享访问签名”。

1. 选择“关闭”。

## <a name="next-steps"></a>后续步骤

现在你已使用“视频分析 - 对象和运动检测”应用程序模板创建了一个 IoT Central 应用程序，为网关设备创建了一个设备模板，并向应用程序中添加了一个网关设备。

如果要使用运行云 VM 的 IoT Edge 模块和模拟视频流试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

如果要使用运行真实设备的 IoT Edge 模块和真实 ONVIF 相机试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
