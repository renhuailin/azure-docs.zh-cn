---
title: 教程 - Azure IoT 连接的废弃物管理 | Microsoft Docs
description: 本教程介绍如何部署和使用适用于 IoT Central 的连接的废弃物管理应用程序模板。
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1ed898b02f2c30c3dcb043903bd3c3261d088539
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183215"
---
# <a name="tutorial-deploy-and-walk-through-the-connected-waste-management-application-template"></a>教程：部署和演练连接的废弃物管理应用程序模板

使用 IoT Central 连接的废弃物管理应用程序模板和本文中的指南来开发端到端连接的废弃物管理解决方案。

:::image type="content" source="media/tutorial-connectedwastemanagement/concepts-connected-waste-management-architecture-1.png" alt-text="连接的废弃物管理体系结构。":::

### <a name="devices-and-connectivity"></a>设备和连接

在开放环境中使用的设备（例如垃圾桶）可以通过低功耗广域网 (LPWAN) 或第三方网络运营商进行连接。 对于这几类设备，请使用 [Azure IoT Central 设备网桥](../core/howto-build-iotc-device-bridge.md)将设备数据发送到 Azure IoT Central 中的 IoT 应用程序。 还可以使用支持 IP 且可直接连接到 IoT Central 的设备网关。

### <a name="iot-central"></a>IoT Central

Azure IoT Central 是一种 IoT 应用平台，可帮助快速构建和部署 IoT 解决方案。 你可以设计、自定义解决方案，并将其与第三方服务相集成。

将智能废弃物设备连接到 IoT Central 时，该应用程序会提供设备命令与控制、监视和警报、带有内置 RBAC 的用户界面、可配置的仪表板和扩展性选项。

### <a name="extensibility-and-integrations"></a>扩展性和集成

可以在 IoT Central 中扩展 IoT 应用程序，并可根据需要执行以下操作：

* 转换和集成 IoT 数据以进行高级分析，例如，通过从 IoT Central 应用程序持续导出的数据训练机器学习模型。
* 通过 Power Automate 或 IoT Central 应用程序中的 Webhook 来触发操作，在其他系统中自动执行工作流。
* 通过 IoT Central API 以编程方式访问 IoT Central 中的 IoT 应用程序。

### <a name="business-applications"></a>业务应用程序

可以使用 IoT 数据为废弃物治理公用事业中的各种业务应用程序赋能。 例如，在连接的废弃物管理解决方案中，可以优化垃圾收集车的调度。 可以基于已连接的垃圾箱中的 IoT 传感器数据执行优化。在 [IoT Central 连接的废弃物管理应用程序](./tutorial-connected-waste-management.md)中，你可以配置规则和操作，并将其设置为在[已连接的现场服务](/dynamics365/field-service/connected-field-service)中创建警报。 在 IoT Central 规则中配置 Power Automate，以跨应用程序和服务自动执行工作流。 此外，基于已连接的现场服务中的服务活动，可以将信息发送回 Azure IoT Central。

可以使用 IoT Central 和已连接的现场服务轻松配置以下集成过程：

* Azure IoT Central 可以将有关设备异常的信息发送到已连接的现场服务进行诊断。
* 连接的现场服务可以创建从设备异常触发的案例或工作订单。
* 连接的现场服务可以安排技术人员进行检查，以防止停机事件发生。
* 可以使用相关服务和计划信息更新 Azure IoT Central 设备仪表板。

在本教程中，你将了解：

> [!div class="checklist"]

> * 使用 Azure IoT Central 联网废弃物管理模板来创建应用。
> * 浏览并自定义仪表板。 
> * 浏览联网垃圾箱设备模板。
> * 浏览模拟设备。
> * 浏览并配置规则。
> * 配置作业。
> * 自定义应用程序品牌。

## <a name="prerequisites"></a>先决条件

* 无需满足特定的先决条件，就可以部署此应用。
* 你可以使用免费定价计划或使用 Azure 订阅。

## <a name="create-connected-waste-management-application"></a>创建连接的废弃物管理应用程序

1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“政府”选项卡 ：:::image type="content" source="media/tutorial-connectedwastemanagement/iot-central-government-tab-overview.png" alt-text="连接的废弃物管理模板":::

1. 选择“连接的废弃物管理”下的“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序

以下各部分将介绍应用程序的主要功能：

### <a name="dashboard"></a>仪表板 

部署应用程序模板之后，默认仪表板为“Wide World 废弃物管理仪表板”。

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-1.png" alt-text="Wide World 废弃物管理仪表板的屏幕截图。":::


生成人员可在仪表板上创建和自定义视图，以供操作员使用。 首先，来看看这个仪表板。 

>[!NOTE]
>仪表板中显示的所有数据均基于模拟设备数据，下一部分将详细介绍。 

仪表板包含不同的磁贴：

* **Wide World Waste 公司图像磁贴**：仪表板中的第一个磁贴是虚构的废弃物公司“Wide World Waste”的图像磁贴。 可自定义该磁贴并放置自己的图像，也可将其删除。 

* **垃圾箱图像磁贴**：可使用图像和内容磁贴创建正在受监视的设备的可视化表示形式以及描述。 

* **填充级别 KPI 磁贴**：该磁贴显示垃圾箱中的填充级别传感器报告的值。 可远程监控垃圾箱中的填充级别和其他传感器（例如“气味测量仪”或“重量”） 。 操作员可采取行动，比如调度垃圾收集车。 

* **废弃物监视区域地图**：该磁贴使用 Azure Maps，你可直接在 Azure IoT Central 中对其进行配置。 地图磁贴显示设备[位置](../core/howto-use-location-data.md)。 尝试将鼠标悬停在地图上，然后尝试使用地图上的控件，如放大、缩小或展开。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-map.png" alt-text="联网废弃物管理模板仪表板地图的屏幕截图。":::



* **填充、气味、重量水平条形图**：可在条形图中直观呈现一种或多种设备遥测数据。 还可以展开条形图。  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-bar-chart.png" alt-text="连接的废弃物管理模板仪表板条形图的屏幕截图。":::


* **Field Services**：仪表板上有一个链接，可通过它了解如何从 Azure IoT Central 应用程序与 Dynamics 365 Field Services 进行集成。 例如，可使用 Field Services 创建用于调度垃圾收集服务的票证。 

### <a name="customize-the-dashboard"></a>自定义仪表板 

可通过选择“编辑”菜单来自定义仪表板  。 然后，可添加新磁贴或配置现有磁贴。 下面是仪表板在编辑模式下的显示效果： 

:::image type="content" source="media/tutorial-connectedwastemanagement/edit-dashboard.png" alt-text="编辑模式下联网废弃物管理模板仪表板的屏幕截图。":::


还可选择“+新建”来创建新的仪表板，并从头开始配置。 你可以有多个仪表板，还可通过仪表板菜单在仪表板之间切换。 

### <a name="explore-the-device-template"></a>浏览设备模板

Azure IoT Central 中的设备模板会定义设备的功能，这些功能可以包括遥测、属性或命令。 构建者可定义设备模板来表示要联网的设备功能。 

联网废弃物管理应用程序附带了一个联网垃圾箱设备示例模板。

如需查看设备模板：

1. 在 Azure IoT Central 中，从应用的左侧窗格中选择“设备模板”。 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template.png" alt-text="显示应用程序中的设备模板列表的屏幕截图。":::


1. 在“设备模板”列表中，选择“联网垃圾箱” 。

1. 检查设备模板功能。 你可以看到，它定义了填充级别、气味测量仪、重量和位置等传感器   。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-connected-bin.png" alt-text="显示连接的垃圾桶设备模板详细信息的屏幕截图。":::


### <a name="customize-the-device-template"></a>自定义设备模板

尝试自定义以下内容：

1. 在设备模板菜单中，选择“自定义”  。
1. 查找“气味测量仪”遥测类型。
1. 将气味测量仪的显示名称更新为“气味级别”  。
1. 尝试更新度量单位，或设置“最小值”和“最大值” 。
1. 选择“保存”。  

### <a name="add-a-cloud-property"></a>添加云属性 

下面介绍如何操作：

1. 从设备模板菜单中，选择“云属性”  。
1. 选择“+添加云属性”。 在 Azure IoT Central 中，可添加与设备相关但不应由设备发送的属性。 例如，云属性可以是特定于安装区域、资产信息或维护信息的警报阈值。 
1. 选择“保存”。 
 
### <a name="views"></a>视图 

联网垃圾箱设备模板带有预定义的视图。 浏览视图，并根据需要对其进行更新。 视图定义了操作员查看设备数据及输入云属性的方式。 

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-views.png" alt-text="连接的废弃物管理模板设备模板视图的屏幕截图。":::


### <a name="publish"></a>发布 

如果进行了任何更改，请记得发布设备模板。 

### <a name="create-a-new-device-template"></a>创建新设备模板 

若要创建新的设备模板，请选择“+新建”，然后按照步骤操作。 可从头开始创建自定义设备模板，也可从 Azure 设备目录选择一个设备模板。 

### <a name="explore-simulated-devices"></a>浏览模拟设备

在 Azure IoT Central 中，可以创建模拟设备以测试设备模板和应用程序。 

联网废弃物管理应用程序有两个模拟设备关联到联网垃圾箱设备模板。 

### <a name="view-the-devices"></a>查看设备

1. 在 Azure IoT Central 的左侧窗格中，选择“设备”。 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices.png" alt-text="联网垃圾箱管理模板设备的屏幕截图。":::


1. 选择“联网垃圾箱”设备。  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices-bin-1.png" alt-text="联网垃圾箱管理模板设备属性的屏幕截图。":::


1. 请转到“云属性”选项卡。将“箱满警报阈值”的值从 95 更新到 100  。 

浏览“设备属性”和“设备仪表板”选项卡 。 

> [!NOTE]
> 所有选项卡都是从设备模板视图配置的。

### <a name="add-new-devices"></a>添加新设备

可选择“设备”选项卡上的“+新建”来添加新设备 。 

## <a name="explore-and-configure-rules"></a>浏览并配置规则

在 Azure IoT Central 中，可创建规则来自动监视设备遥测，并在满足一个或多个条件时触发操作。 这些操作可能包括发送电子邮件通知、触发 Power Automate 中的操作，或启动 Webhook 操作来向其他服务发送数据。

“连接的废弃物管理”应用程序有四个示例规则  。

### <a name="view-rules"></a>查看规则

1. 在 Azure IoT Central 的左侧窗格中，选择“规则”。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-rules.png" alt-text="联网垃圾箱管理模板规则的屏幕截图。":::


1. 选择“箱满警报”。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-bin-full-alert.png" alt-text="箱满警报的屏幕截图。":::


 1. 箱满警报会检查以下情况：**填充级别大于或等于箱满警报阈值**。

    箱满警报阈值是联网垃圾箱设备模板中定义的一个云属性。 

现在，让我们创建电子邮件操作。

### <a name="create-an-email-action"></a>创建电子邮件操作

在规则的“操作”列表中，可配置电子邮件操作：
1. 选择“+ 电子邮件”  。 
1. 对于“显示名称”，输入“高 pH 警报” 。
1. 对于“收件人”，输入与 Azure IoT Central 帐户关联的电子邮件地址。 
1. （可选）输入要包含在电子邮件文本中的注释。
1. 选择“完成” > “保存” 。 

满足配置条件后，现在会收到电子邮件。

>[!NOTE]
>每次满足条件后，应用程序都会发送电子邮件。 “禁用”该规则会停止从自动规则接收电子邮件  。 
  
若要创建新规则，请从“规则”的左窗格中，选择“+新建” 。

## <a name="configure-jobs"></a>配置作业

在 Azure IoT Central 中，可通过作业在多台设备上触发设备或云属性更新。 还可以使用作业在多个设备上触发设备命令。 Azure IoT Central 将自动执行工作流。 

1. 在 Azure IoT Central 的左侧窗格中，选择“作业”。 
1. 选择“+新建”，并配置一个或多个作业。 

## <a name="customize-your-application"></a>自定义应用程序 

构建者可以更改多个设置来自定义应用程序中的用户体验。

### <a name="change-the-application-theme"></a>更改应用程序主题

下面介绍如何操作：
1. 转到“管理” > “自定义应用程序” 。
1. 选择“更改”，来选择要上传用于应用程序徽标的图像 。
1. 选择“更改”，来选择要上传用于浏览器图标的图像（该图标是一个将显示在浏览器标签页上的图像） 。
1. 还可添加 HTML 十六进制颜色代码来替换默认浏览器颜色。 为此，请使用“标头”和“强调”字段 。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-customize-your-application.png" alt-text="联网废弃物管理模板的“自定义应用程序”的屏幕截图。":::


1. 还可更改应用程序图像。 选择“管理” > “应用程序设置” > “选择图像”，来选择要上传用作应用程序图像的图像  。
1. 最后，还可单击应用程序刊头上的“设置”来更改主题。

## <a name="clean-up-resources"></a>清理资源

如果不希望继续使用此应用程序，请删除应用程序，步骤如下：

1. 在 Azure IoT Central 应用的左侧窗格中选择“管理”。
1. 选择“应用程序设置” > “删除” 。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接的水消耗量概念](./tutorial-water-consumption-monitoring.md)
