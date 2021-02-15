---
title: 快速入门 - 在 Azure IoT Central 中监视设备
description: 快速入门 - 了解如何以操作员身份使用 Azure IoT Central 应用程序监视设备。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833875"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>快速入门：使用 Azure IoT Central 监视设备

*本文适用于操作员、构建者和管理员。*

本快速入门介绍作为操作员如何使用 Microsoft Azure IoT Central 应用程序监视设备和更改设置。

## <a name="prerequisites"></a>先决条件

在开始之前，应先完成前面的三个快速入门：[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)、[将模拟设备添加到 IoT Central 应用程序](./quick-create-simulated-device.md)以及[为设备配置规则和操作](quick-configure-rules.md)。

## <a name="receive-a-notification"></a>接收通知

Azure IoT Central 将有关设备的通知作为电子邮件发送。 作为构建者，你添加了一条规则，用于在已连接的设备传感器的湿度超过阈值时向操作员发送通知。 作为操作员，你检查电子邮件中的通知。

打开你在[配置用于设备的规则和操作](quick-configure-rules.md)快速入门结束时收到的电子邮件。 在电子邮件中，选择指向设备的链接：

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="显示通知电子邮件的屏幕截图":::

此时将在浏览器中打开前面快速入门中创建的模拟设备的“概述”视图  ：

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="显示触发通知的设备的概述的屏幕截图":::

## <a name="investigate-an-issue"></a>调查问题

作为操作员，你可以在“概述”  、“关于”  和“命令”  视图上查看设备相关信息。 构建者创建了“管理设备”  视图，供你编辑设备信息和设置设备属性。

仪表板上的图表显示了设备湿度的绘图。 你确定设备湿度过高。

## <a name="remediate-an-issue"></a>解决问题

若要对设备进行更改，请使用“管理设备”  页。

将“目标温度”更改为“80”以加热设备并降低湿度。 选择“保存”  以更新设备。 当设备确认设置更改时，属性的状态将更改为“已同步”  ：

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="显示设备的已更新目标温度设置的屏幕截图":::

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 接收通知
* 调查问题
* 解决问题

现在已了解如何监视设备，建议执行的下一步骤是：

> [!div class="nextstepaction"]
> [生成和管理设备模板](howto-set-up-template.md)。
