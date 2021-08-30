---
title: include 文件
description: include 文件
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 78ad23713d1fc58d55696502dc9ff780847a8357
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414859"
---
<!-- put the ## header in the file that includes this file -->

本部分在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)。

1. 在 IoT 中心导航菜单中，打开“IoT 设备”，然后选择“新建”，在 IoT 中心添加设备 。

    ![在门户中创建设备标识](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. 在“创建设备”中，提供新设备的名称（例如 myDeviceId），然后选择“保存”。 此操作会为 IoT 中心创建设备标识。 让“自动生成密钥”保持选中状态，这样将自动生成主密钥和辅助密钥。

   ![添加新设备](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 创建设备后，在“IoT 设备”窗格的列表中打开该设备。 复制“主连接字符串”。 设备代码使用此连接字符串与中心通信。 

    默认情况下，密钥和连接字符串进行了掩码处理，因为它们是敏感信息。 如果单击眼睛图标，它们将按下图所示显示。 使用“复制”按钮进行复制时，无需显示它们。

    ![设备连接字符串](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 它存储设备 ID 和密钥用作安全凭据，以及可用于禁用单个设备访问的启用/禁用标志。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。
