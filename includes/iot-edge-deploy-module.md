---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628882"
---
Azure IoT Edge 的主要功能之一是从云中将代码部署到 IoT Edge 设备。 *IoT Edge 模块* 是以容器形式实现的可执行程序包。 在本部分中，你将直接从 Azure IoT 中心的 [Azure 市场的 IoT Edge 模块部分](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署一个预建的模块。

在本部分中部署的模块模拟一个传感器并发送生成的数据。 开始使用 IoT Edge 时，此模块是非常有用的代码段，因为你可以使用模拟的数据进行开发和测试。 如果要确切了解此模块的功能，则可以查看[模拟的温度传感器源代码](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)。

请按照以下步骤从 Azure 市场部署你的第一个模块。

1. 登录 [Azure 门户](https://portal.azure.com)并转到 IoT 中心。

1. 从左侧菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 选择设备列表中目标设备的设备 ID。

1. 在上方栏中，选择“设置模块”。

   ![显示正在选择“设置模块”的屏幕截图。](./media/iot-edge-deploy-module/select-set-modules.png)

1. 在“IoT Edge 模块”下，打开“添加”下拉菜单，然后选择“市场模块”  。

   ![显示“添加”下拉菜单的屏幕截图。](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. 在 IoT Edge 模块市场中，搜索并选择 `Simulated Temperature Sensor` 模块。

   该模块会添加到“IoT Edge 模块”部分，并处于所需的运行状态。

1. 在完成时选择“下一步:”路由以继续执行向导的下一步。

   ![显示在添加模块后继续下一步的屏幕截图。](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. 在“路由”选项卡上，删除默认路由（路由），然后选择“下一步:   查看 + 创建”继续执行向导的下一步。

   >[!Note]
   >路由使用名称和值对构造而成。 应在此页上看到两条路由。 默认路由（路由）会将所有消息发送到 IoT 中心（称为 `$upstream`）。 从 Azure 市场添加模块时，将自动创建第二条路由 (SimulatedTemperatureSensorToIoTHub)。 此路由用于将所有来自模拟温度模块中的消息发送到 IoT 中心。 可以删除默认路由，因为在这种情况下它是多余的。

   ![显示删除默认路由并转到下一步的屏幕截图。](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. 查看 JSON 文件，然后选择“创建”。 JSON 文件定义你部署到 IoT Edge 设备的所有模块。 你将看到“SimulatedTemperatureSensor”模块以及“edgeAgent”和“edgeHub”这两个运行时模块  。

   >[!Note]
   >在将新部署提交到 IoT Edge 设备时，不会向设备推送任何内容。 相反，设备会定期对 IoT 中心进行查询以获取任何新指令。 如果设备找到了更新的部署清单，则它会使用关于新部署的信息来从云中拉取模块映像，然后开始在本地运行模块。 此过程可能需要几分钟。

1. 创建模块部署详细信息后，向导会将你返回到设备详细信息页。 在“模块”选项卡上查看部署状态。

   应会看到三个模块：$edgeAgent、$edgeHub 和 SimulatedTemperatureSensor  。 如果其中一个或多个模块在“在部署中指定”下为“是”，但在“由设备报告”下不是，则意味着 IoT Edge 设备仍在启动它们  。 等待几分钟，然后刷新页面。

   ![显示已部署模块列表中模拟温度传感器的屏幕截图。](./media/iot-edge-deploy-module/view-deployed-modules.png)
