---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 8b48aacf29666536a360af34b5cbc0f6a2dcf0f2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111993611"
---
## <a name="create-the-cloud-components"></a>创建云组件

### <a name="create-the-iot-central-application"></a>创建 IoT Central 应用程序

可以通过多种方式将设备连接到 Azure IoT。 本部分介绍如何使用 Azure IoT Central 连接设备。 IoT Central 是一个 IoT 应用程序平台，可降低创建和管理 IoT 解决方案的成本和复杂性。

要创建新的应用程序：
1. 从 [Azure IoT Central 门户](https://apps.azureiotcentral.com/)的侧面导航菜单中选择 **“编译”** 。

    > [!NOTE]
    > 如果有现成的 IoT Central 应用程序，则可以用来完成本文中的步骤，而不必创建新的应用程序。 在这种情况下，如果要使用现有的设备 ID，我们建议你创建一个新设备，或者删除设备，然后重新创建设备。

1. 在 **“自定义应用”** 磁贴中选择 **“创建应用”** 。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-select-custom.png" alt-text="在 Azure IoT Central 中创建自定义应用的屏幕截图":::

1. 添加应用程序名称和 URL。
1. 选择“免费”定价计划以激活 7 天试用版。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-custom.png" alt-text="在 Azure IoT Central 中输入新应用的信息的屏幕截图":::

1. 选择“创建”  。 IoT Central 在预配应用程序后，会自动将你重定向到新的应用程序仪表板。


### <a name="create-a-new-device"></a>创建新设备

在本部分中，将使用 IoT Central 应用程序仪表板创建新设备。 在后面的部分中，将使用新创建的设备的连接信息安全连接到你的物理设备。

创建设备：
1. 在应用程序仪表板中，选择侧面导航菜单中的“设备”。
1. 从 **“所有设备”** 窗格中选择 **“创建设备”** 以打开 **“新建设备”** 窗口。 （如果要重复使用已有一个或多个设备的现有应用程序，请选择 **“+ 新建”** 以打开该窗口。）
1. 将“设备模板”保持设为“未分配”。
1. 填写所需的设备名称和设备 ID。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-device.png" alt-text="在 Azure IoT Central 中输入新设备的信息的屏幕截图":::

1. 选择“创建”按钮。
1. 新创建的设备显示在“所有设备”列表中。  选择设备名称以显示详细信息。
1. 在右上方的菜单栏中选择“连接”可显示连接信息（用于在下一节中配置设备）。

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-device-connection-info.png" alt-text="Azure IoT Central 中的设备连接详细信息的屏幕截图":::

1. 请注意，“连接”对话框中显示的以下连接字符串参数的连接值。 将在下一步中将这些值添加到配置文件中：

    * `ID scope`
    * `Device ID`
    * `Primary key`