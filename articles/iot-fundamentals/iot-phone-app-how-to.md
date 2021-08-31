---
title: 使用 Smartphone 作为 Azure IoT 设备
description: 本操作指南介绍如何使用 Azure IoT 即插即用应用将 Smartphone 转换为 IoT 设备。
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: how-to
ms.date: 05/27/2021
ms.author: dobett
ms.openlocfilehash: 0daffdab23d5da069598512f1934cfdc04bf01b3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112305893"
---
# <a name="how-to-turn-your-smartphone-into-an-iot-device"></a>如何将 Smartphone 转换为 IoT 设备

Azure IoT 解决方案使你能够将 IoT 设备连接到基于云的 IoT 服务。 设备发送遥测数据（如温度和湿度），并对命令（如重启和更改传递间隔）做出响应。 设备还可以将其内部状态与服务同步，并共享设备型号和操作系统等属性。

IoT 即插即用手机应用使你能够快速开始浏览 Azure IoT 功能，而无需配置专用 IoT 设备。

## <a name="azure-iot-plug-and-play-app"></a>Azure IoT 即插即用应用

为了快速入门，本文将使用 Smartphone 应用作为 IoT 设备。 该应用将发送从手机传感器收集的遥测数据，响应从服务调用的命令，并报告属性值。

可以使用此 Smartphone 应用来执行以下操作：

- 探索基本 IoT 方案。
- 远程管理手机并与之进行交互。
- 测试配置。
- 作为自定义设备开发的起点。

## <a name="install-the-app"></a>安装应用

[!INCLUDE [iot-phoneapp-install](../../includes/iot-phoneapp-install.md)]

## <a name="app-features"></a>应用功能

### <a name="connect"></a>连接

可以通过扫描 IoT Central 中的 QR 码来连接到 IoT Central 应用程序。

若要了解详细信息，请参阅本指南后面的[连接应用](#connect-the-app)。

### <a name="telemetry"></a>遥测

此应用从手机传感器收集数据，以将其作为遥测数据发送到你所使用的 IoT 服务。 默认情况下，每隔五秒聚合一次传感器数据，但你可以在“应用设置”页上更改此值：

:::image type="content" source="media/iot-phone-app-how-to/telemetry.png" alt-text="Smartphone 应用中的“遥测数据”页的屏幕截图。":::

以下屏幕截图显示了 IoT Central 中显示某些设备遥测数据的设备视图：

:::image type="content" source="media/iot-phone-app-how-to/central-telemetry.png" alt-text="IoT Central 中设备遥测数据的屏幕截图。":::

### <a name="properties"></a>属性

应用报告设备型号和制造商等设备状态。 还有一个可编辑的属性，可在 Azure IoT 解决方案中修改和查看更改同步：

:::image type="content" source="media/iot-phone-app-how-to/properties.png" alt-text="显示移动设备应用中的“属性”页的屏幕截图。":::

以下屏幕截图显示了在将属性发送到设备后 IoT Central 中的可写属性：

:::image type="content" source="media/iot-phone-app-how-to/central-writable-property.png" alt-text="在 IoT Central 中显示可写属性的屏幕截图。":::

### <a name="image-upload"></a>图像上传

IoT Central 和 IoT 中心均支持从设备上将文件上传到 Azure 存储。 该 Smartphone 应用允许你从设备上传图像。

若要详细了解如何配置服务以支持从设备上传文件，请参阅：

- [通过 IoT 中心将设备中的文件上传到云](../iot-hub/iot-hub-csharp-csharp-file-upload.md)。
- [通过 IoT Central 将设备中的文件上传到云](../iot-central/core/howto-configure-file-uploads.md)。

:::image type="content" source="media/iot-phone-app-how-to/image-upload.png" alt-text="显示 Smartphone 应用中的“图像上传”页的屏幕截图。":::

### <a name="logs"></a>日志

Smartphone 应用会将事件写入本地日志文件，你可以在应用中查看这些事件。 使用日志文件进行故障排除并更好地了解应用的作用：

:::image type="content" source="media/iot-phone-app-how-to/logs.png" alt-text="显示 Smartphone 应用中的“日志”页的屏幕截图。":::

### <a name="settings"></a>设置

应用中的“设置”页允许你执行以下操作：

- 将应用连接到 Azure IoT 解决方案。
- 查看当前设备注册信息。
- 通过清除存储的数据重置应用。
- 自定义应用外观。
- 设置应用向 IoT 服务发送遥测数据的频率。

:::image type="content" source="media/iot-phone-app-how-to/settings.png" alt-text="Smartphone 应用中的“设置”页的屏幕截图。":::

## <a name="connect-the-app"></a>连接应用

### <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

<!-- To do: does this need an app template? -->
创建 IoT Central 应用程序。 若要了解详细信息，请参阅[创建 IoT Central 应用程序](../iot-central/core/howto-create-iot-central-application.md)。

### <a name="register-a-device"></a>注册设备

在连接手机应用之前，需要在 IoT Central 应用程序中注册设备。 创建设备注册时，IoT Central 会生成设备连接信息。

在 IoT Central 中注册设备：

1. 登录到 IoT Central 应用程序，然后导航到“设备”页。

1. 选择“创建设备”。

1. 在“创建新设备”页上，选择“创建”：

    :::image type="content" source="media/iot-phone-app-how-to/iot-central-create-device.png" alt-text="显示如何在 IoT Central 中创建设备的屏幕截图。":::

1. 在设备列表中，单击设备名称，然后选择“连接”。 在“设备连接”页上，可以看到要在 Smartphone 应用中扫描的 QR 码：

    :::image type="content" source="media/iot-phone-app-how-to/device-connection-qr-code.png" alt-text="显示带有 QR 码的“设备连接”页的屏幕截图。":::

### <a name="connect-the-device"></a>连接设备

在 IoT Central 中注册设备后，可以通过扫描 QR 码来连接 Smartphone 应用。 连接应用：

1. 在智能手机上打开 IoT PnP 应用。

1. 在欢迎页上，选择“扫描 QR 码”。 将手机摄像头对准 QR 码。 然后在建立连接时等待几秒钟。

1. 在应用的“遥测”页上，可以看到应用发送到 IoT Central 的数据。 在“日志”页上，可以看到设备正在建立连接，还可以看到一些初始化消息。

1. 在“设置 > 注册”页上，可以看到设备 ID 和应用用于连接到 IoT Central 的 ID 范围。

若要详细了解如何将设备连接到 IoT Central，请参阅[连接到 Azure IoT Central](../iot-central/core/concepts-get-connected.md)。

### <a name="verify-the-connection"></a>验证连接

若要查看设备在 IoT Central 应用程序中发送的数据：

1. 登录到 IoT Central 应用程序，然后导航到“设备”页。 你的设备已自动分配到“Smartphone”设备模板。

    > [!TIP]
    > 可能需要在 Web 浏览器中刷新页面，才能看到设备被分配到 Smartphone 设备模板。

1. 在设备列表中，单击设备名称，然后选择“概述”。 “概述”页显示 Smartphone 传感器的遥测数据：

    :::image type="content" source="media/iot-phone-app-how-to/smartphone-overview.png" alt-text="IoT Central 中显示 Smartphone 传感器遥测数据的设备“概述”页的屏幕截图。":::

1. 查看“关于”页查看设备发送的属性。

1. 在“命令”页上，运行“LightOn”命令以打开手机闪光灯。

> [!TIP]
> “原始数据”页显示来自设备的所有数据。

## <a name="next-steps"></a>后续步骤

现在，你已将 Smartphone 应用连接到 IoT Central，接下来建议详细了解 [IoT Central](../iot-central/core/overview-iot-central.md)。
