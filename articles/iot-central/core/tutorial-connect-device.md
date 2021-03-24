---
title: 教程 - 将泛型客户端应用连接到 Azure IoT Central | Microsoft Docs
description: 本教程介绍如何以设备开发人员的身份将运行 C、C#、Java、JavaScript 或 Python 客户端应用的设备连接到 Azure IoT Central 应用程序。 通过添加支持操作员与连接的设备进行交互的视图来修改自动生成的设备模板。
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8f1b5eabe235d107b48dc7b2db5b6d4b1188a3fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833960"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序

本文适用于解决方案构建者和设备开发人员。 

本教程介绍如何以设备开发人员的身份将客户端应用程序连接到 Azure IoT Central 应用程序。 该应用程序模拟恒温器设备的行为。 当应用程序连接到 IoT Central 时，它将发送恒温器设备模型的模型 ID。 IoT Central 使用模型 ID 检索设备模型，并创建设备模板。 可以向设备模板添加自定义和视图，使操作员能够与设备进行交互。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建并运行设备代码，并查看它是否可连接到 IoT Central 应用程序。
> * 查看从设备发送的模拟遥测数据。
> * 将自定义视图添加到设备模板。
> * 发布设备模板。
> * 使用视图管理设备属性。
> * 调用命令来控制设备。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>查看原始数据

作为设备开发人员，你可以使用“原始数据”视图检查设备发送到 IoT Central 的原始数据：

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="原始数据视图":::

在此视图中，你可以选择要显示的列，并设置要查看的时间范围。 “未建模数据”列显示设备中与设备模板中的任何属性或遥测定义不匹配的数据。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

若要继续浏览 IoT Central 系列教程并详细了解如何构建 IoT Central 解决方案，请参阅：

> [!div class="nextstepaction"]
> [创建网关设备模板](./tutorial-define-gateway-device-type.md)

作为设备开发人员，现在你已了解了如何使用 Java 创建设备的基础知识，一些建议的后续步骤是：

* 要详细了解在实现设备代码时设备模板的作用，请阅读[什么是设备模板？](./concepts-device-templates.md)。
* 阅读[连接到 Azure IoT Central](./concepts-get-connected.md)，详细了解如何向 IoT Central 注册设备以及 IoT Central 如何保护设备连接。
* 阅读[遥测、属性和命令有效负载](concepts-telemetry-properties-commands.md)，详细了解设备与 IoT Central 交换的数据。
* 阅读 [IoT 即插即用设备开发人员指南](../../iot-pnp/concepts-developer-guide-device.md)。
