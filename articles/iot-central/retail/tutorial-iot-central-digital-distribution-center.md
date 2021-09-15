---
title: 教程 - Azure IoT 数字分发中心 | Microsoft Docs
description: 本教程介绍如何部署和使用 IoT Central 的数字分发中心应用程序模板
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 71d16e08501a345fcc6ae8435f0609660be9a18a
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195232"
---
# <a name="tutorial-deploy-and-walk-through-the-digital-distribution-center-application-template"></a>教程：部署并演练数字分发中心应用程序模板

使用 IoT Central 数字分发中心应用程序模板和本文中的指南来开发端到端数字分发中心解决方案。

   :::image type="content" source="media/tutorial-iot-central-ddc/digital-distribution-center-architecture.png" alt-text="数字分发中心。":::

1. 一组将遥测数据发送到网关设备的 IoT 传感器
2. 将遥测和聚合见解发送到 IoT Central 的网关设备
3. 数据被路由到所需的 Azure 服务以进行操作
4. 可以使用 Azure 服务（如 ASA 或 Azure Functions）重新设置数据流的格式，并将其发送到所需的存储帐户
5. 处理后的数据将存储在热存储中以便进行准实时操作，或者存储在冷存储中以用于执行更多基于 ML 或批处理分析的见解增强功能。 
6. 可使用逻辑应用为最终用户商业应用程序中的各种业务工作流提供支持

### <a name="video-cameras"></a>摄像机 

摄像机是这个数字连接的企业级生态系统中的主要传感器。 利用机器学习和人工智能领域的进步，可将视频转换为结构化数据，在边缘进行处理，然后发送到云。 我们可以使用 IP 照相机来捕获图像，在照相机上压缩图像，然后通过边缘计算将压缩的数据发送到视频分析管道，或使用 GigE 视觉照相机捕获传感器上的图像，将这些图像直接发送到 Azure IoT Edge，进行压缩并在视频分析管道中处理。 

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge 网关

“相机即传感器”和边缘工作负荷由 Azure IoT Edge 在本地管理，相机流由分析管道进行处理。 Azure IoT Edge 的视频分析处理管道带来了许多好处（包括响应时间缩短、带宽消耗降低），从而降低了延迟以实现快速数据处理。 只会将最重要的元数据、见解或操作发送到云，以进行进一步的操作或调查。 

### <a name="device-management-with-iot-central"></a>使用 IoT Central 进行设备管理
 
Azure IoT Central 是一种解决方案开发平台，可简化 IoT 设备和 Azure IoT Edge 网关连接、配置和管理。 该平台大大降低了 IoT 设备管理、运营和相关开发的负担和成本。 客户和合作伙伴可以构建端到端的企业解决方案，以便在分发中心中实现数字反馈循环。

### <a name="business-insights-and-actions-using-data-egress"></a>使用数据出口获得业务见解并执行操作 

IoT Central 平台通过连续数据导出 (CDE) 和 API 提供了丰富的扩展选项。 通常将基于遥测数据处理或原始遥测的业务见解导出到首选业务线应用程序。 可以通过 Webhook、服务总线、事件中心或 Blob 存储来构建、训练和部署机器学习模型并进一步丰富见解，从而实现这一目标。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 创建数字分发中心应用程序。
> * 演练应用程序。

## <a name="prerequisites"></a>先决条件

* 无需满足特定的先决条件，就可以部署此应用
* 建议使用 Azure 订阅，但不使用也可以

## <a name="create-digital-distribution-center-application-template"></a>创建数字分发中心应用程序模板

使用以下步骤创建应用程序：

1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“零售”选项卡 ：

   :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-home-page.png" alt-text="显示如何创建应用的屏幕截图。":::

1. 在“数字分发中心应用程序”下选择“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序 

以下各部分将介绍应用程序的主要功能：

### <a name="dashboard"></a>仪表板

默认仪表板是一个以分发中心操作员为中心的门户。 Northwind Trader 是一家虚构的分发中心解决方案提供商，他们管理着传送带系统。 

在此仪表板中，你将看到一个网关和一台充当 IoT 设备的摄像机。 网关将提供有关包的遥测（例如有效、无效、未标识和大小）以及相关设备孪生属性。 所有下游命令都在 IoT 设备（例如照相机）上执行。 此仪表板已预先配置为展示关键的分发中心设备操作活动。

仪表板以逻辑方式组织，用于显示 Azure IoT 网关和 IoT 设备的设备管理功能。  

* 可以执行网关命令和控制任务
* 管理解决方案中的所有摄像机。
* 管理解决方案中的所有摄像机。
* 管理解决方案中的所有摄像机。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-dashboard.png" alt-text="显示数字分发中心仪表板的屏幕截图。":::

### <a name="device-template"></a>设备模板

单击“设备模板”选项卡，你将看到网关功能模型。 功能模型是围绕两个不同的接口“摄像机”和“数字分发网关”构建的  

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="显示应用程序中数字分发网关设备模板的屏幕截图。":::

**照相机** - 此接口组织所有特定于照相机的命令功能 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="显示数字分发网关设备模板中相机接口的屏幕截图。":::

**数字分发网关** - 此接口表示来自相机、云定义的设备孪生属性和网关信息的所有遥测。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="显示数字分发网关设备模板中数字分发网关接口的屏幕截图。":::

### <a name="gateway-commands"></a>网关命令

此接口组织了所有网关命令功能

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="显示数字分发网关设备模板中网关命令接口的屏幕截图。":::

### <a name="rules"></a>规则

选择“规则”选项卡以查看此应用程序模板中存在的两个不同规则。 这些规则配置为通过电子邮件将通知发送给操作员以进行进一步调查。

 **无效包裹过多警报** - 摄像机检测到大量无效包裹通过传送带系统时，将触发此规则。

**大型包裹** - 如果摄像机检测到无法检查其质量的大型包裹，则将触发此规则。 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-rules.png" alt-text="显示数字分发中心应用程序中规则列表的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请访问“管理” > “应用程序设置”并单击“删除”，以删除应用程序模板    。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-cleanup.png" alt-text="显示如何在使用应用程序后将其删除的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

详细了解数字分发中心解决方案体系结构：

> [!div class="nextstepaction"]
> [数字分发中心概念](./architecture-digital-distribution-center.md)
