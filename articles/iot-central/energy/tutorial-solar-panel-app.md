---
title: 教程 - Azure IoT 太阳能板监视 | Microsoft Docs
description: 本教程介绍如何部署和使用 IoT Central 的太阳能板监视应用程序模板。
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59650af808a5af947cbb2fd6df1f203692fb7034
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182719"
---
# <a name="tutorial-deploy-and-walk-through-the-solar-panel-monitoring-app-template"></a>教程：部署和演练太阳能板监视应用模板 

使用 IoT Central 太阳能板监视应用程序模板和本文中的指南来开发端到端太阳能板监视解决方案。

  :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-app-architecture.png" alt-text="太阳能板体系结构。":::

该体系结构包括以下组件。 某些应用程序可能不需要此处列出的每个组件。

### <a name="solar-panels-and-connectivity"></a>太阳能电池板和连接

太阳能板是可再生能源的重要来源之一。 根据太阳能电池板的类型和设置，可以使用网关或其他中间设备和专用系统来连接它。 可能需要构建 IoT Central 设备网桥以连接无法直接连接的设备。 IoT Central 设备桥是一种开放源代码解决方案，你可以在[此处](../core/howto-build-iotc-device-bridge.md)找到完整的详细信息。 

### <a name="iot-central-platform"></a>IoT Central 平台

Azure IoT Central 是一个平台，可简化 IoT 解决方案的构建，并有助于减轻 IoT 管理、运营和开发的负担以及降低相关成本。 借助 IoT Central，可以轻松地大规模连接、监视和管理物联网 (IoT) 资产。 将太阳能电池板连接到 IoT Central 之后，应用模板将使用设备模型、命令和仪表板等内置功能。 该应用模板还将 IoT Central 存储用于暖路径方案，例如近实时仪表数据监视、分析、规则和可视化效果。

### <a name="extensibility-options-to-build-with-iot-central"></a>使用 IoT Central 进行构建的可扩展性选项

IoT Central 平台提供了两个扩展选项：连续数据导出 (CDE) 和 API。 客户和合作伙伴可以在这些选项之间进行选择，以针对特定需求自定义其解决方案。 例如我们的某个合作伙伴为 Azure Data Lake Storage (ADLS) 配置了 CDE。 他们将 ADLS 用于长期数据保留和其他冷路径存储方案（例如批处理、审核和报告目的）。 


在本教程中，你将了解：

> [!div class="checklist"]

> * 免费创建太阳能板应用
> * 演练应用程序
> * 清理资源


## <a name="prerequisites"></a>先决条件

* 无需满足特定的先决条件，就可以部署此应用。
* 你可以使用免费定价计划或使用 Azure 订阅。


## <a name="create-a-solar-panel-monitoring-application"></a>创建太阳能板监视应用程序


1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“能源”选项卡 ：

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-build.png" alt-text="智能计量模板":::

1. 在“太阳能板监视”下，选择“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序

以下各部分将介绍应用程序的主要功能：

### <a name="dashboard"></a>仪表板

部署应用程序模板后，你需要多浏览一下应用。 请注意，它附带了示例智能仪表设备、设备型号和仪表板。

Adatum 是一家虚构的能源公司，负责监视和管理太阳能板。 在太阳能板监视仪表板上，可以看到太阳能板属性、数据和示例命令。 通过此仪表板，你或你的支持团队可在问题需要额外支持前主动执行以下活动：
* 查看地图上的最新面板信息及其安装[位置](../core/howto-use-location-data.md)。
* 检查面板状态和连接状态。
* 查看能源产生和温度趋势，以捕获任何异常模式。
* 跟踪计划和计费用途的总能耗产生。
* 如有必要，激活面板并更新固件版本。 在模板中，命令按钮显示可能的功能，并且不发送实际命令。

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png" alt-text="太阳能板监视模板仪表板的屏幕截图。":::

### <a name="devices"></a>设备

该应用附带了一个示例太阳能板设备。 若要查看设备详细信息，请选择“设备”。

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device.png" alt-text="太阳能板监视模板设备的屏幕截图。":::

选择示例设备 SP0123456789。 从“更新属性”选项卡中，可更新设备的可写属性，并直观查看仪表板上已更新的值。 

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png" alt-text="太阳能板监视模板的“更新属性”选项卡的屏幕截图。":::


### <a name="device-template"></a>设备模板

若要查看太阳能板设备型号，请选择“设备模板”选项卡。模型为数据、属性、命令和视图预定义了接口。

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png" alt-text="太阳能板监视模板设备模板的屏幕截图。":::


## <a name="clean-up-resources"></a>清理资源

如果决定不继续使用此应用程序，请使用以下步骤将它删除：

1. 在左侧窗格中，选择“管理”。
1. 选择“应用程序设置” > “删除” 。 

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png" alt-text="太阳能板监视模板管理的屏幕截图。":::
