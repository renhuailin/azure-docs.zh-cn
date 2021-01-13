---
title: 如何从 IoT 中心将设备移到 Azure IoT Central
description: 如何从 IoT 中心将设备移到 Azure IoT Central
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146441"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>如何从 IoT 中心将设备传输到 Azure IoT Central

*本文适用于操作员和设备开发人员。*  

本文介绍如何将设备从 IoT 中心传输到 Azure IoT Central 应用程序。 

设备首先连接到 DPS 终结点，以检索连接到应用程序所需的信息。 IoT Central 应用程序在内部使用 IoT 中心来处理设备连接。  

设备可以使用连接字符串或使用 DPS 直接连接到 IoT 中心。 [Azure IoT 中心设备预配服务 (DPS) ](../../iot-dps/about-iot-dps.md) 是 IoT Central 的路由。

## <a name="to-move-the-device"></a>移动设备

若要将设备连接到 IOT 中心的 IoT Central，需要使用以下内容更新设备：

* IoT Central 应用程序的 [作用域 ID](../../iot-dps/concepts-service.md) 。
* 派生自[组 SAS](concepts-get-connected.md)密钥或[x.509 证书](../../iot-hub/iot-hub-x509ca-overview.md)的密钥

若要与 IoT Central 进行交互，必须有一个设备模板，该模板对设备实现的属性/遥测/命令进行建模。 有关详细信息，请参阅 [连接到 IoT Central](concepts-get-connected.md) 和 [设备模板是什么？](concepts-device-templates.md)

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员，则建议执行以下后续步骤：

- 查看一些演示如何使用 SAS 令牌的示例代码，请参阅 [教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)
- 了解如何 [使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解如何 [在 Azure IoT Central 应用程序中定义新的 IoT 设备类型](./howto-set-up-template.md)
- 了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)