---
title: Azure IoT Central 中的体系结构概念 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 的体系结构相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 724c7e65d04b635dcaa635d29483da756cf47bfc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675854"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 体系结构

本文提供 IoT Central 解决方案体系结构中关键元素的概述。

:::image type="content" source="media/concepts-architecture/architecture.png" alt-text="IoT Central 解决方案的概要体系结构" border="false":::

IoT Central 应用程序：

- 让你可以管理解决方案中的 IoT 设备。
- 让你可以查看和分析来自设备的数据。
- 可以导出到作为解决方案一部分的其他服务并与这些服务集成。

## <a name="iot-central"></a>IoT Central

IoT Central 是用于 IoT 解决方案开发的现成环境。 它是一个平台即服务 (PaaS) IoT 解决方案，主界面是一个 Web UI。 它还提供一个 [REST API](#rest-api)，使你能够以编程方式来与应用程序交互。

本部分介绍 IoT Central 应用程序的关键功能。

### <a name="manage-devices"></a>管理设备

使用 IoT Central 可以管理向解决方案发送数据的 [IoT 设备](#devices)群。 例如，可以：

- 控制哪些设备可以[连接](concepts-get-connected.md)到你的应用程序，以及它们如何进行身份验证。
- 使用[设备模板](concepts-device-templates.md)来定义可连接到你的应用程序的设备类型。
- 通过对连接的设备设置属性或调用命令来管理设备。 例如，为恒温器设备设置目标温度属性，或调用命令来触发设备以更新其固件。 可采用以下方式设置属性和调用命令：
  - 通过[可自定义](concepts-device-templates.md#views)的 Web UI（针对单个设备）。
  - 使用计划的[作业](howto-manage-devices-in-bulk.md)或按需作业（针对多个设备）。
- 维护[设备元数据](concepts-device-templates.md#cloud-properties)，例如客户地址或上次服务日期。

### <a name="view-and-analyze-data"></a>查看和分析数据

在 IoT Central 应用程序中，可以查看和分析单个设备的数据，或来自多个设备的聚合数据：

- 使用设备模板为特定类型的单个设备定义[自定义视图](howto-set-up-template.md#views)。 例如，可以绘制单个恒温器在一段时间内的温度的图表，或显示货车的实时位置。
- 使用内置[分析](tutorial-use-device-groups.md)查看多个设备的聚合数据。 例如，可以查看多个零售店的总租用率，或识别租用率最高或最低的店铺。
- 创建自定义[仪表板](howto-manage-dashboards.md)来帮助你管理设备。 例如，可以添加地图、磁贴和图表来显示设备遥测数据。  

### <a name="secure-your-solution"></a>保护解决方案

在 IoT Central 应用程序中，可以管理解决方案的以下安全方面：

- [设备连接](concepts-get-connected.md)：创建、撤销和更新由设备用来与应用程序建立连接的安全密钥。
- [应用集成](howto-authorize-rest-api.md#get-an-api-token)：创建、撤销和更新由其他应用程序用来与你的应用程序建立安全连接的安全密钥。
- [用户管理](howto-manage-users-roles.md)：管理可登录到应用程序的用户，以及管理用于确定这些用户拥有的权限的角色。
- [组织](howto-create-organizations.md)：定义一个层次结构，用于管理哪些用户可以查看 IoT Central 应用程序中的哪些设备。

### <a name="rest-api"></a>REST API

构建集成，使其他应用程序和服务能够管理你的应用程序。 例如，以编程方式[管理应用程序中的设备](howto-control-devices-with-rest-api.md)，或将[用户信息](howto-manage-users-roles-with-rest-api.md)与外部系统同步。

## <a name="devices"></a>设备

设备从传感器收集数据，并将其作为遥测数据流发送到 IoT Central 应用程序。 例如，制冷单元发送温度值流，或者货车以流数据的形式传送其位置信息。

设备可以使用属性来报告其状态，例如阀门是处于打开还是关闭状态。 IoT Central 应用程序还可以使用属性来设置设备状态，例如设置恒温器的目标温度。

IoT Central 还可以通过对设备调用命令来控制设备。 例如，指示设备下载并安装固件更新。

设备实现的[遥测、属性和命令](concepts-telemetry-properties-commands.md)统称为设备功能。 可以在设备和 IoT Central 应用程序之间共享的模型中定义这些功能。 在 IoT Central 中，此模型是用于定义特定设备类型的设备模板的一部分。

[设备实现](tutorial-connect-device.md)应遵循 [IoT 即插即用约定](../../iot-develop/concepts-convention.md)，以确保它能够与 IoT Central 通信。 有关详细信息，请参阅各种语言的 [SDK 和示例](../../iot-develop/libraries-sdks.md)。

设备使用受支持的协议之一连接到 IoT Central：[MQTT、AMQP 或 HTTP](../../iot-hub/iot-hub-devguide-protocols.md)。

## <a name="gateways"></a>网关

本地设备网关在多种情况下都很有用，例如：

- 设备可能无法直接连接到 IoT Central，因为它们无法连接到 Internet。 例如，你可能有一系列支持蓝牙的占用传感器，它们需要通过网关进行连接。
- 设备生成的数据量可能很高。 为了降低成本，可以先合并或聚合本地网关中的数据，然后再将其发送到 IoT Central 应用程序。
- 解决方案可能需要对数据中的异常情况快速做出响应。 可以在网关上运行规则，以识别异常情况并在本地执行操作，而无需将数据发送到 IoT Central 应用程序。

若要了解详细信息，请参阅[将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序](concepts-iot-edge.md)。

## <a name="data-export"></a>数据导出

尽管 IoT Central 具有内置分析功能，但你可以将数据导出到其他服务和应用程序。 导出数据的原因包括：

### <a name="storage-and-analysis"></a>存储和分析

为实现长期存储并控制存档和保留策略，可以[将数据连续导出](howto-export-data.md)到其他存储目标。 使用单独的存储还可以使用其他分析工具来得出见解和查看解决方案中的数据。

### <a name="business-automation"></a>业务自动化

IoT Central 中的[规则](howto-configure-rules-advanced.md)让你可以触发外部操作，例如发送电子邮件或触发事件，以响应 IoT Central 中的状况。 例如，可以在设备的环境温度达到阈值时通知工程师。

### <a name="additional-computation"></a>其他计算

在 IoT Central 或其他服务中使用数据之前，可能需要先对数据[进行转换或执行计算](howto-transform-data.md)。 例如，可以将当地天气信息添加到货车报告的位置数据。

## <a name="next-steps"></a>后续步骤

了解 Azure IoT Central 的体系结构后，建议接下来了解 Azure IoT Central 中的[设备连接](concepts-get-connected.md)。
