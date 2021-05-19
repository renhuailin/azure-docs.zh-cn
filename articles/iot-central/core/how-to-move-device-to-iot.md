---
title: 如何从 IoT 中心将设备移到 Azure IoT Central
description: 如何从 IoT 中心将设备移到 Azure IoT Central
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 543c3f1c72857098540cc2a77e8a0093b907b799
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210829"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>如何从 IoT 中心将设备转移到 Azure IoT Central

本文适用于操作员和设备开发人员。  

本文介绍如何从 IoT 中心将设备传转移到 Azure IoT Central 应用程序。 

设备首先连接到 DPS 终结点，以检索设备连接到应用程序所需的信息。 在内部，IoT Central 应用程序使用 IoT 中心来处理设备连接。  

设备可以使用连接字符串或 DPS 直接连接到 IoT 中心。 [Azure IoT 中心设备预配服务 (DPS)](../../iot-dps/about-iot-dps.md) 是 IoT Central 的路由。

## <a name="to-move-the-device-to-azure-iot-central"></a>将设备移动到 Azure IoT Central

若要从 IoT 中心将设备连接到 IoT Central，需要使用以下内容更新设备：

* IoT Central 应用程序的[范围 ID](../../iot-dps/concepts-service.md)。
* 派生自[组 SAS](concepts-get-connected.md) 密钥或 [X.509 证书](../../iot-hub/iot-hub-x509ca-overview.md)的密钥

若要与 IoT Central 进行交互，必须使用一个设备模板对设备实现的属性/遥测/命令进行建模。 有关详细信息，请参阅[连接到 IoT Central](concepts-get-connected.md) 和[什么是设备模板？](concepts-device-templates.md)

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员，则建议执行以下后续步骤：

- 在[教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)中查看一些介绍如何使用 SAS 令牌的示例代码
- 了解[如何使用用于 IoT Central 应用程序的 Node.js 设备 SDK 通过 X.509 证书连接设备](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解如何[在 Azure IoT Central 应用程序中定义新的 IoT 设备类型](./howto-set-up-template.md)
- 了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)