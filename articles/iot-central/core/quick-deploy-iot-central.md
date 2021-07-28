---
title: 快速入门 - 创建和使用 Azure IoT Central 应用程序 | Microsoft Docs
description: 快速入门 - 创建新的 Azure IoT Central 应用程序并连接你的第一台设备。 本快速入门使用来自 Google Play 或 Apple App Store 的智能手机应用作为 IoT 设备。
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4e40c079c50ccb7f83a4e03d47cf3e9b419870ef
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288473"
---
# <a name="quickstart---create-an-azure-iot-central-application-and-use-your-smartphone-to-send-telemetry"></a>快速入门 - 创建 Azure IoT Central 应用程序并使用智能手机发送遥测数据

本快速入门展示了如何创建 Azure IoT Central 应用程序并连接你的第一台设备。 为了快速入门，你需要在智能手机上安装一个应用程序来充当设备。 该应用将发送遥测数据、报告属性并响应命令：

:::image type="content" source="media/quick-deploy-iot-central/overview.png" alt-text="将智能手机应用连接到 IoT Central 的快速入门方案概述。" border="false":::

## <a name="prerequisites"></a>先决条件

一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!TIP]
> 你的 Azure 订阅中应至少具有参与者访问权限。 如果你自己创建了订阅，你将自动成为具有足够访问权限的管理员。 若要了解详细信息，请参阅[什么是 Azure 基于角色的访问控制？](../../role-based-access-control/overview.md)

一台 Android 或 iOS 手机，你可以在其上安装来自官方应用商店的免费应用。

## <a name="create-an-application"></a>创建应用程序

导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用与你的 Azure 订阅关联的 Microsoft 个人、工作或学校帐户登录。

IoT Central 提供各种行业相关应用程序模板来帮助你入门。 本快速入门使用自定义应用程序模板从头开始创建应用程序：

1. 导航到“生成”页，然后选择“自定义应用”磁贴中的“创建应用”  ：

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="生成 IoT 应用程序页面":::

1. 在“新建应用程序”页上，确保在“应用程序模板”下选择了“自定义应用程序”  。

1. Azure IoT Central 会根据所选应用程序模板自动建议应用程序名称。 输入自己的应用程序名称，例如“Contoso 快速入门应用”。

1. Azure IoT Central 还会根据应用程序名称为你生成唯一的 URL 前缀。 使用此 URL 访问应用程序。 如果你愿意，可以将此 URL 前缀更改为更令人难忘的内容。 该 URL 必须是唯一的。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central 的“创建应用程序”页":::

1. 对于本快速入门，请将定价计划设置为“标准 2”。

1. 在“Azue 订阅”下拉列表中选择你的订阅。

1. 在“位置”下拉列表中选择离你最近的位置。

1. 查看“条款和条件”，并选择页面底部的“创建”  。 几秒钟后，IoT Central 应用程序便可供使用：

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central 应用程序":::

## <a name="register-a-device"></a>注册设备

若要将设备连接到 IoT Central 应用程序，需要一些连接信息。 获取此连接信息的简单方法是注册你的设备。

注册设备：

1. 在 IoT Central 中，导航到“设备”页，然后选择“创建设备” ：

    :::image type="content" source="media/quick-deploy-iot-central/create-device.png" alt-text="显示在 IoT Central 中创建设备的屏幕截图。":::

1. 在“创建新设备”页中，接受默认设置，然后选择“创建”。

1. 在设备列表中，单击设备名称：

    :::image type="content" source="media/quick-deploy-iot-central/device-name.png" alt-text="显示你可以选择的设备名称的屏幕截图（这些设备名称被突出显示）。":::

1. 在设备页面，选择“连接”，然后选择“QR 码” ：

    :::image type="content" source="media/quick-deploy-iot-central/device-registration.png" alt-text="显示可用于连接手机应用的 QR 码的屏幕截图。":::

请将此页保持打开状态， 在下一部分中，你将使用手机应用来扫描此 QR 码，以将手机连接到 IoT Central。

## <a name="connect-your-device"></a>连接设备

为了快速入门，本文将 IoT 即插即用智能手机应用作为 IoT 设备使用。 该应用将发送从手机传感器收集的遥测数据，响应从 IoT Central 调用的命令，并将属性值报告给 IoT Central。

[!INCLUDE [iot-phoneapp-install](../../../includes/iot-phoneapp-install.md)]

要将 IoT 即插即用应用连接到 Iot Central 应用程序：

1. 在智能手机上打开 IoT PnP 应用。

1. 在欢迎页上，选择“扫描 QR 码”。 将手机摄像头对准 QR 码。 然后在建立连接时等待几秒钟。

1. 在应用的“遥测”页上，可以看到应用发送到 IoT Central 的数据。 在“日志”页上，可以看到设备正在建立连接，还可以看到一些初始化消息。

从 IoT Central 中的智能手机应用中查看遥测数据：

1. 在 IoT Central 中，导航到“设备”页。

1. 在设备列表中，单击设备名称，然后选择“概述”：

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-telemetry.png" alt-text="包含遥测绘图的“概述”页的屏幕截图。":::

> [!TIP]
> 智能手机应用只在屏幕打开时发送数据。
## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 IoT Central 应用程序并连接了发送遥测数据的设备。 在本快速入门中，你使用了智能手机应用作为连接到 IoT Central 的 IoT 设备。 接下来，建议执行以下步骤来继续了解 IoT Central：

> [!div class="nextstepaction"]
> [向 IoT Central 应用程序添加规则](./quick-configure-rules.md)
