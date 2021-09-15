---
title: 教程 - Azure IoT 智能库存管理 | Microsoft Docs
description: 本教程介绍如何部署和使用适用于 IoT Central 的智能库存管理应用程序模板
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 747bcaffd1e24937580dcf95f352a34f66c3314e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437501"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-inventory-management-application-template"></a>教程：部署和演练智能库存管理应用程序模板

使用 IoT Central 智能库存管理应用程序模板和本文中的指南来开发端到端智能库存管理解决方案。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-architecture.png" alt-text="智能库存管理。":::

1. 一组将遥测数据发送到网关设备的 IoT 传感器
2. 将遥测和聚合见解发送到 IoT Central 的网关设备
3. 数据被路由到所需的 Azure 服务以进行操作
4. 可以使用 Azure 服务（如 ASA 或 Azure Functions）重新设置数据流的格式，并将其发送到所需的存储帐户 
5. 处理后的数据将存储在热存储中以便进行近实时操作，或者存储在冷存储中以用于执行更多基于 ML 或批处理分析的见解增强功能。 
6. 可使用逻辑应用为最终用户商业应用程序中的各种业务工作流提供支持

### <a name="details"></a>详细信息

以下部分概述了射频识别 (RFID)、低耗电蓝牙 (BLE) 标记的概念体系结构遥测摄取的每个部分

### <a name="rfid-tags"></a>RFID 标记

RFID 标记通过无线电波发送相关项的数据。 除非指定，否则 RFID 标签通常没有电池。 标记从读取器产生的无线电波中接收能源，并将信号发送回 RFID 读取器。

### <a name="ble-tags"></a>BLE 标记

能源信标定期广播数据包。 BLE 读取器或 Smartphone 上已安装的服务会检测到信标数据，然后将其传输到云端。

### <a name="rfid--ble-readers"></a>RFID 和 BLE 读取器

RFID 读取器将无线电波转换为更有用的数据形式。 然后，将从标签收集的信息存储在本地边缘服务器中，或者通过 MQTT 使用 JSON-RPC 2.0 发送到云。
BLE 读取器也称为接入点 (AP)，与 RFID 读取器类似。 它用于检测附近的蓝牙信号，并通过 MQTT 使用 JSON-RPC 2.0 将其消息中继到本地 Azure IoT Edge 或云。
许多读取器能够读取 RFID 和信标信号，并提供与温度、湿度、加速度计和陀螺仪相关的附加传感器功能。

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge 网关

Azure IoT Edge 服务器提供了一个位置，可在其中对本地数据进行预处理，再将其发送到云。 我们还可以使用标准容器部署云工作负荷人工智能、Azure 和第三方服务、业务逻辑。

### <a name="device-management-with-iot-central"></a>使用 IoT Central 进行设备管理 

Azure IoT Central 是一种解决方案开发平台，可简化 IoT 设备连接、配置和管理。 该平台大大降低了 IoT 设备管理、运营和相关开发的负担和成本。 客户和合作伙伴可以构建端到端的企业解决方案，以便在库存管理中实现数字反馈循环。

### <a name="business-insights--actions-using-data-egress"></a>使用数据出口获得业务见解并执行操作 

IoT Central 平台通过连续数据导出 (CDE) 和 API 提供了丰富的扩展选项。 通常将基于遥测数据处理或原始遥测的业务见解导出到首选业务线应用程序。 可以使用 Webhook、服务总线、事件中心或 Blob 存储来构建、训练和部署机器学习模型并进一步丰富见解，从而实现这一目标。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 创建智能库存管理应用程序 
> * 演练应用程序 

## <a name="prerequisites"></a>先决条件

* 无需满足特定的先决条件，就可以部署此应用
* 建议使用 Azure 订阅，但不使用也可以

## <a name="create-smart-inventory-management-application"></a>创建智能库存管理应用程序

使用以下步骤创建应用程序：

1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“零售”选项卡 ：:::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc-retail-home-page.png" alt-text="显示如何基于智能库存管理应用程序模板创建应用的屏幕截图":::

1. 在“智能库存管理”下选择“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序

以下各部分将介绍应用程序的主要功能：

### <a name="dashboard"></a>仪表板 

成功部署应用模板后，默认仪表板是一个以智能库存管理操作员为中心的门户。 Northwind Trader 是一家虚构的智能库存提供商，使用低耗电蓝牙 (BLE) 管理仓库并使用射频识别 (RFID) 管理零售商店。 在此仪表板中，你将看到两个不同的网关，它们提供有关库存的遥测数据以及可执行的相关命令、作业和操作。 此仪表板已预先配置为展示关键的库存管理设备操作活动。
仪表板在逻辑上区分两个不同的网关设备管理操作， 
   * 仓库在托盘上部署了固定的 BLE 网关和 BLE 标记，以在大型设备上监测和跟踪库存
   * 零售商店通过在每个商品级别使用固定的 RFID 网关和 RFID 标签来实现监测和跟踪商店中的库存
  * 查看网关的[位置](../core/howto-use-location-data.md)、状态和相关详细信息 

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-1.png" alt-text="显示智能库存管理仪表板的上半部分的屏幕截图。":::

    * 可以轻松地跟踪网关、活动和未知标记的总数。
    * 可以执行设备管理操作，例如更新固件、禁用传感器、启用传感器、更新传感器阈值、更新遥测间隔时间和更新设备服务协定
    * 网关设备可以通过完整或增量扫描来执行按需库存管理。

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-2.png" alt-text="显示智能库存管理仪表板的下半部分的屏幕截图。":::

### <a name="device-template"></a>设备模板

单击“设备模板”选项卡，你将看到网关功能模型。 功能模型是围绕两个不同的接口（“网关遥测与属性”以及“网关命令”）构建的  

**网关遥测与属性** - 此接口表示与传感器、位置、设备信息和设备孪生属性功能（例如网关阈值和更新间隔）有关的所有遥测。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-1.png" alt-text="显示应用程序中库存网关设备模板的屏幕截图。":::

**网关命令** - 此接口组织了所有网关命令功能

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-2.png" alt-text="显示库存网关设备模板中网关命令接口的屏幕截图。":::

### <a name="rules"></a>规则

选择“规则”选项卡以查看此应用程序模板中存在的两个不同规则。 这些规则配置为通过电子邮件将通知发送给操作员以进行进一步调查。

**离线网关**：如果网关长时间未向云报告，则会触发此规则。 网关可能会因低电量模式、连接丢失、设备运行状况而无响应。

**未知标记**：跟踪与资产关联的每个 RFID 和 BLE 标签至关重要。 如果网关检测到过多未知标签，则表明标签源应用程序存在同步问题。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-rules.png" alt-text="显示智能库存管理应用程序中规则列表的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请访问“管理” > “应用程序设置”并单击“删除”，以删除应用程序模板    。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-cleanup.png" alt-text="显示如何在使用应用程序后将其删除的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

详细了解智能库存管理：

> [!div class="nextstepaction"]
> [智能库存管理概念](./architecture-smart-inventory-management.md)
