---
title: 使用 Azure IoT Central 构建的水质监测解决方案的参考体系结构 | Microsoft Docs
description: 了解使用 Azure IoT Central 构建的水质监测解决方案的概念。
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 83906682a188a0d6abf859589a38311a0d726a05
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229805"
---
# <a name="water-quality-monitoring-reference-architecture"></a>水质检测参考体系结构 

可以将 Azure IoT Central 应用模板用作快速入门 IoT 应用程序来构建水质监测解决方案  。 本文提供有关构建端到端解决方案的概要性参考体系结构指南。 

![水质监测体系结构](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

概念：

1. 设备和连接  
1. IoT Central
1. 扩展性和集成
1. 业务应用程序

本文介绍通常在水质监测解决方案中发挥作用的关键组件。

## <a name="devices-and-connectivity"></a>设备和连接

水务管理解决方案使用智能水务设备，例如流量计、水质监测仪、智能阀门、检漏仪。

智能水务解决方案中使用的设备可以通过低功耗广域网 (LPWAN) 或第三方网络运营商进行连接。 对于这几类设备，请使用 [Azure IoT Central 设备网桥](../core/howto-build-iotc-device-bridge.md)将设备数据发送到 Azure IoT Central 中的 IoT 应用程序。 还可以使用支持 IP 且可直接连接到 IoT Central 的设备网关。

## <a name="iot-central"></a>IoT Central

Azure IoT Central 是一个 IoT 应用平台，可帮助快速构建和部署 IoT 解决方案。 你可以设计、自定义解决方案，并将其与第三方服务相集成。

将智能水务设备连接到 IoT Central 时，该应用程序会提供设备命令与控制、监视和警报、带有内置 RBAC 的用户界面、可配置的仪表板和扩展性选项。

## <a name="extensibility-and-integrations"></a>扩展性和集成

可以在 IoT Central 中扩展 IoT 应用程序，并可根据需要执行以下操作：

* 转换和集成 IoT 数据以进行高级分析，例如，通过从 IoT Central 应用程序持续导出的数据训练机器学习模型。
* 通过 Power Automate 或 IoT Central 应用程序中的 Webhook 来触发操作，在其他系统中自动执行工作流。
* 通过 IoT Central API 以编程方式访问 IoT Central 中的 IoT 应用程序。

## <a name="business-applications"></a>业务应用程序

可以使用 IoT 数据为水务治理公用事业中的各种业务应用程序赋能。 在 [IoT Central 水消耗量监测应用程序](tutorial-water-consumption-monitoring.md)中，可以配置规则和操作并将其设置为在[已连接的现场服务](/dynamics365/field-service/connected-field-service)中创建警报。 在 IoT Central 规则中配置 Power Automate，以跨应用程序和服务自动执行工作流。 此外，基于已连接的现场服务中的服务活动，可以将信息发送回 Azure IoT Central。

## <a name="next-steps"></a>后续步骤

* 了解如何[创建水质监测](./tutorial-water-quality-monitoring.md) IoT Central 应用程序
* 详细了解[政府用 IoT Central 模板](./overview-iot-central-government.md)
* 若要详细了解 IoT Central，请参阅 [IoT Central 概述](../core/overview-iot-central.md)
