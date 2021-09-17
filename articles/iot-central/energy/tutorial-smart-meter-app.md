---
title: 教程 - Azure IoT 智能计量监视 | Microsoft Docs
description: 本教程介绍如何部署和使用 IoT Central 的智能计量监视应用程序模板。
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a332ab10ce4e7c38442288165c56d1161081cd9c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179441"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-meter-monitoring-app-template"></a>教程：部署并演练智能计量监视应用模板 

使用 IoT Central 智能计量监视应用程序模板和本文中的指南来开发端到端智能计量监视解决方案。

  :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-app-architecture.png" alt-text="智能计量体系结构。":::

该体系结构包括以下组件。 一些解决方案可能不需要此处列出的所有组件。

### <a name="smart-meters-and-connectivity"></a>智能仪表和连接

智能仪表是所有能源资产中最重要的设备之一。 它记录能源消耗数据并将其传输到用于监视和其他用例（如计费和需求响应）的实用程序。 根据仪表类型，它可以通过网关或其他中间设备或系统（例如边缘设备和前端系统）连接到 IoT Central。 构建 IoT Central 设备网桥以连接无法直接连接的设备。 IoT Central 设备桥是一种开放源代码解决方案，你可以在[此处](../core/howto-build-iotc-device-bridge.md)找到完整的详细信息。 

### <a name="iot-central-platform"></a>IoT Central 平台

Azure IoT Central 是一个平台，可简化 IoT 解决方案的构建，并有助于减轻 IoT 管理、运营和开发的负担以及降低相关成本。 借助 IoT Central，可以轻松地大规模连接、监视和管理物联网 (IoT) 资产。 将智能仪表连接到 IoT Central 之后，应用模板使用设备模型、命令和仪表板等内置功能。 该应用模板还将 IoT Central 存储用于暖路径方案，例如近实时仪表数据监视、分析、规则和可视化效果。 

### <a name="extensibility-options-to-build-with-iot-central"></a>使用 IoT Central 进行构建的可扩展性选项

IoT Central 平台提供了两个扩展选项：连续数据导出 (CDE) 和 API。 客户和合作伙伴可以在这些选项之间进行选择，以针对特定需求自定义其解决方案。 例如我们的某个合作伙伴为 Azure Data Lake Storage (ADLS) 配置了 CDE。 他们将 ADLS 用于长期数据保留和其他冷路径存储方案（例如批处理、审核和报告目的）。

在本教程中，你将了解：

- 免费创建智能计量应用
- 应用程序演练
- 清理资源

## <a name="prerequisites"></a>先决条件

* 无需满足特定的先决条件，就可以部署此应用。
* 你可以使用免费定价计划或使用 Azure 订阅。

## <a name="create-a-smart-meter-monitoring-application"></a>创建智能计量监视应用程序

1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“能源”选项卡 ：

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-build.png" alt-text="智能计量模板":::

1. 在“智能计量监视”下，选择“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序

以下各部分将介绍应用程序的主要功能：

### <a name="dashboard"></a>仪表板

部署应用程序模板之后，附带了示例智能计量设备、设备型号和仪表板。 

Adatum 是一家虚构的能源公司，负责监视和管理智能计量。 在智能计量监视仪表板上，可以看到智能计量属性、数据和示例命令。 它使操作员和支持团队能够在转变为支持事件之前主动执行以下活动： 
* 查看地图上的最新计量信息及其安装[位置](../core/howto-use-location-data.md)
* 主动检查计量网络和连接状态 
* 监视网络运行状况的最小和最大电压读数 
* 查看能源、电源和电压趋势，以捕获任何异常模式 
* 跟踪计划和计费用途的总能耗
* 命令和控制操作，例如重新连接计量和更新固件版本。 在模板中，命令按钮显示可能的功能，并且不发送实际命令。 

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png" alt-text="智能计量监视仪表板。":::

### <a name="devices"></a>设备

该应用附带了一个示例智能测量设备。 单击“设备”  选项卡可以看到设备详细信息。

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-devices.png" alt-text="智能计量设备。":::

单击示例设备“SM0123456789”  链接以查看设备详细信息。 可以在“更新属性”页上更新设备的可写属性，并在仪表板上可视化更新的值  。

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png" alt-text="智能计量属性。":::

### <a name="device-template"></a>设备模板

单击“设备模板”  选项卡以查看智能计量设备型号。 模型为数据、属性、命令和视图预定义了接口。

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-template.png" alt-text="智能计量设备模板。":::

## <a name="clean-up-resources"></a>清理资源

如果决定不继续使用此应用程序，请使用以下步骤删除应用程序：

1. 在左侧窗格中，打开“管理”选项卡
1. 选择“应用程序设置”，然后单击页面底部的“删除”按钮。 

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png" alt-text="删除应用程序。":::

## <a name="next-steps"></a>后续步骤

> [教程：部署并演练太阳能板应用程序模板](tutorial-solar-panel-app.md)

