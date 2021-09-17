---
title: 教程 - Azure IoT 店内分析 | Microsoft Docs
description: 本教程演示如何部署和使用“在 IoT Central 中创建店内分析零售应用程序”。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 08/17/2021
ms.openlocfilehash: 93438e9726dca4fe74da3bbfc9bd06533115e74e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439027"
---
# <a name="tutorial-deploy-and-walk-through-the-in-store-analytics-application-template"></a>教程：部署和演练店内分析应用程序模板

使用 IoT Central 店内分析应用程序模板和本文中的指南来开发端到端的店内分析解决方案。

:::image type="content" source="media/tutorial-in-store-analytics-create-app/store-analytics-architecture-frame.png" alt-text="Azure IoT Central 商店分析。":::

- 一组将遥测数据发送到网关设备的 IoT 传感器
- 将遥测和聚合见解发送到 IoT Central 的网关设备
- 连续数据导出到所需的 Azure 服务以进行操作
- 可按所需格式对数据进行结构化并将其发送到存储服务
- 业务应用程序可以查询数据并生成可助力零售运营的见解

让我们了解店内分析解决方案中常用的一些关键组件。

## <a name="condition-monitoring-sensors"></a>环境监视传感器

IoT 解决方案的基础是从零售商店环境内捕获有意义信号的一组传感器。 上面的体系结构图最左侧的各种传感器反映了这一点。

## <a name="gateway-devices"></a>网关设备

许多 IoT 传感器可将原始信号直接送到云或位于其附近的网关设备。 网关设备先在边缘执行数据聚合，然后将摘要见解发送到 IoT Central 应用程序。 网关设备还负责将命令和控制操作中继到传感器设备（如果适用）。 

## <a name="iot-central-application"></a>IoT Central 应用程序

Azure IoT Central 应用程序从各种 IoT 传感器以及零售商店环境中的网关设备引入数据，并生成一组有意义的见解。

Azure IoT Central 还为商店运营商提供定制的体验，使他们能够远程监视和管理基础结构设备。

## <a name="data-transform"></a>数据转换

解决方案中的 Azure IoT Central 应用程序可配置为将原始或聚合见解导出到一组 Azure PaaS（平台即服务）服务，这些服务可执行数据操作并丰富这些见解，然后将其放入业务应用程序。 

## <a name="business-application"></a>业务应用程序

IoT 数据可用于为零售环境中部署的各种业务应用程序提供支持。 零售店经理或员工可以使用这些应用程序将业务见解可视化，并实时采取有意义的行动。 若要了解如何为零售团队构建实时 Power BI 仪表板，请按照[教程](./tutorial-in-store-analytics-create-app.md)操作。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
>
> - 使用 Azure IoT Central“店内分析 - 结帐”模板来创建零售商店应用程序 
> - 自定义应用程序设置
> - 创建和自定义 IoT 设备模板
> - 将设备连接到应用程序
> - 添加规则和操作来监视条件

## <a name="prerequisites"></a>先决条件

- 无需满足特定的先决条件，就可以部署此应用。
- 你可以使用免费定价计划或使用 Azure 订阅。

## <a name="create-in-store-analytics-application"></a>创建店内分析应用程序

使用以下步骤创建应用程序：

1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“零售”选项卡 ：:::image type="content" source="media/tutorial-in-store-analytics-create-app/iotc-retail-homepage.png" alt-text="联网物流模板":::

1. 在“店内分析 - 结帐”下选择“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序

以下各部分将介绍应用程序的主要功能：

### <a name="customize-application-settings"></a>自定义应用程序设置

你可以更改多个设置来自定义应用程序中的用户体验。 在本部分中，你将选择预定义的应用程序主题。 你还可以了解如何创建自定义主题，以及如何更新应用程序图像。 使用自定义主题可以设置刊头中显示的应用程序浏览器颜色、浏览器图标和应用程序徽标。

若要选择预定义的应用程序主题，请执行以下操作：

1. 在刊头上选择“设置”  。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/settings-icon.png" alt-text="Azure IoT Central 应用程序设置。":::

2. 选择新的“主题”  。

3. 选择“保存”。 

可以创建自定义主题，而不是使用预定义的主题。 如果要使用一组示例图像自定义应用程序并完成本教程，请下载 [Contoso 示例图像](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)。

若要创建自定义主题，请执行以下操作：

1. 展开左窗格（如果尚未展开）。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/dashboard-expand.png" alt-text="Azure IoT Central 左窗格。":::

1. 选择“管理”>“自定义应用程序”  。

1. 使用“更改”按钮选择要作为应用程序徽标上传的图像   。 （可选）为徽标替换文字  指定一个值。 

1. 使用“更改”按钮选择将显示在浏览器选项卡上的浏览器图标图像   。

1. （可选）通过添加 HTML 十六进制颜色代码来替换默认浏览器颜色  。 对于标题，添加 #008575   。  对于主题色，添加 #A1F3EA   。 

1. 选择“保存”。  

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/select-application-logo.png" alt-text="Azure IoT Central 自定义徽标。":::

    保存后，应用程序将更新浏览器颜色、刊头中的徽标以及浏览器图标。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/saved-application-settings.png" alt-text="Azure IoT Central 更新的应用程序设置。":::

若要更新应用程序图像，请执行以下操作：

1. 选择“管理”>“应用程序设置”  。

1. 使用“选择图像”按钮选择要作为应用程序图像上传的图像  。 此图像显示在 IoT Central 应用程序管理器的“我的应用”  页中的应用程序磁贴上。

1. 选择“保存”。 

1. （可选）导航到 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上的“我的应用”视图  。 应用程序磁贴将显示更新的应用程序图像。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/customize-application-image.png" alt-text="Azure IoT Central 自定义应用程序图像。":::

### <a name="create-device-templates"></a>创建设备模板

你可以创建使你和应用程序操作员能够配置和管理设备的设备模板。 可以通过以下方式创建模板：生成自定义模板，导入现有模板文件，或从 Azure IoT 设备目录导入模板。 创建并自定义设备模板后，使用该模板将实际设备连接到应用程序。 （可选）使用设备模板生成用于测试的模拟设备。

 “店内分析 - 结帐”应用程序模板包含多个设备的设备模板。  有设备模板可用于应用程序中使用的三个设备中的两个设备。 RuuviTag 设备模板不包含在“店内分析 - 结帐”应用程序模板中  。 在本部分中，你将 RuuviTag 传感器的设备模板添加到应用程序。

若要将 RuuviTag 设备模板添加到应用程序，请执行以下操作：

1. 在左窗格中选择“设备模板”  。

1. 选择“+ 新建”以创建新的设备模板  。

1. 在 Azure IoT 设备目录中查找并选择“RuuviTag 多传感器”设备模板。 

1. 选择“下一步:自定义”。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png" alt-text="突出显示“下一步:自定义”按钮的屏幕截图。":::

1. 选择“创建”  。 应用程序将添加 RuuviTag 设备模板。

1. 在左窗格中选择“设备模板”  。 页面将显示应用程序模板中包含的所有设备模板以及刚刚添加的 RuuviTag 设备模板。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/device-templates-list.png" alt-text="Azure IoT Central RuuviTag 传感器设备模板。":::

### <a name="customize-device-templates"></a>自定义设备模板

可以通过三种方式自定义应用程序中的设备模板。 第一种方式是通过更改设备功能来自定义设备中的本机内置界面。 例如，使用温度传感器可以更改温度界面的显示名称、数据类型、度量单位、最小和最大工作范围等详细信息。 

第二种方式是通过添加云属性来自定义设备模板。 云属性不属于内置设备功能。 云属性是 Azure IoT Central 应用程序创建、存储并与设备关联的自定义数据。 云属性的示例可以是计算值，也可以是要与一组设备关联的位置等元数据。

第三种方式是通过生成自定义视图来自定义设备模板。 视图为操作员提供一种可视化设备的遥测和元数据（如设备指标和运行状况）的方法。

此处，使用前两种方法来自定义 RuuviTag 传感器的设备模板。

若要自定义 RuuviTag 设备模板的内置界面，请执行以下操作：

1. 在左窗格中选择“设备模板”  。 

1. 选择 RuuviTag 传感器的模板。 

1. 隐藏左窗格。 模板的摘要视图将显示设备功能。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png" alt-text="Azure IoT Central RuuviTag 设备模板摘要视图。":::

1. 在 RuuviTag 设备模板菜单中选择“自定义”  。 

1. 在功能列表中滚动并查找 `humidity` 遥测类型。 它是具有湿度的可编辑显示名称值的行项   。

在以下步骤中，你将为 RuuviTag 传感器自定义 `humidity` 遥测类型。 （可选）自定义一些其他遥测类型。

对于 `humidity` 遥测类型，进行以下更改：

1. 选择“展开”控件以展开该行的架构详细信息  。

1. 将“湿度”中的显示名称更新为自定义值，例如“相对湿度”    。

1. 将“语义类型”选项从“无”更改为“湿度”    。  （可选）在展开的架构视图中设置湿度遥测类型的架构值。 架构设置允许你为传感器跟踪的数据创建详细的验证要求。例如，可以为给定界面设置最小和最大工作范围值。

1. 选择“保存”  以保存更改。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png" alt-text="显示“自定义”屏幕并突出显示“保存”按钮的屏幕截图。":::

若要将云属性添加到应用程序中的设备模板，请执行以下操作：

1. 在 RuuviTag 设备模板菜单中选择“云属性”  。

1. 选择“添加云属性”  。 

指定以下值以创建自定义属性来存储每个设备的位置：

1. 输入“显示名称”的“位置”值   。 此值将自动复制到“名称”  字段，它是属性的友好名称。 可以使用复制的值或对其进行更改。

1. 在“架构”下拉列表中选择“字符串”   。 使用字符串类型可以基于模板将位置名称字符串与任何设备关联。 例如，可以将应用商店中的某个区域与每个设备关联。

1. 将“最小长度”设置为“2”   。 

1. 将“修整空白”设置为“打开”   。

1. 选择“保存”  以保存自定义云属性。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png" alt-text="Azure IoT Central RuuviTag 设备模板自定义。":::

1. 选择“发布”  。 

    发布设备模板使其对应用程序操作员可见。 发布模板后，可以使用它来生成用于测试的模拟设备，或者将实际设备连接到应用程序。 如果已将设备连接到应用程序，则发布自定义模板会将更改推送到设备。

### <a name="add-devices"></a>添加设备

创建并自定义设备模板后，就可以添加设备了。 

对于本教程，使用以下一组实际和模拟设备来生成应用程序：

- 实际 Rigado C500 网关
- 两个实际 RuuviTag 传感器
- 模拟占用传感器  。 模拟传感器包含在应用程序模板中，因此不需要创建它。 

> [!NOTE]
> 如果没有实际设备，仍可以通过创建模拟 RuuviTag 传感器来完成本教程。 以下说明包含创建模拟 RuuviTag 的步骤。 不需要创建模拟网关。

完成以下两篇文章中的步骤以连接实际 Rigado 网关和 RuuviTag 传感器。 完成后，请返回到本教程。 由于你已在本教程中创建了设备模板，因此不需要按照以下一组说明再次创建设备模板。

- 若要连接 Rigado 网关，请参阅[将 Rigado Cascade 500 连接到 Azure IoT Central 应用程序](../core/howto-connect-rigado-cascade-500.md)。
- 若要连接 RuuviTag 传感器，请参阅[将 RuuviTag 传感器连接到 Azure IoT Central 应用程序](../core/howto-connect-ruuvi.md)。 还可以使用这些说明创建两个模拟传感器（如果需要）。

### <a name="add-rules-and-actions"></a>添加规则和操作

使用 Azure IoT Central 应用程序中的传感器监视条件时，可以创建规则以在满足某些条件时运行操作。 规则与设备模板和一个或多个设备关联，并且包含必须根据设备遥测或事件满足的条件。 规则也有一个或多个关联的操作。 这些操作可能包括发送电子邮件通知，或触发 Webhook 操作以向其他服务发送数据。  “店内分析 - 结帐”应用程序模板包括应用程序中设备的一些预定义规则。

在本部分中，你将创建一个新规则，用于根据 RuuviTag 传感器遥测检查最大相对湿度级别。 向该规则添加操作，以便在湿度超过最大值时，应用程序发送电子邮件。 

若要创建规则，请执行以下操作： 

1. 展开左窗格。

1. 选择“规则”。 

1. 选择“+ 新建”  。

1. 输入“湿度级别”  作为规则的名称。 

1. 选择“范围”  中的 RuuviTag 设备模板。 你定义的规则将基于该模板应用于所有传感器。 或者，你可以创建仅将规则应用于已定义的传感器子集的筛选器。 

1. 选择 `Relative humidity` 作为“遥测”  。 这是在上一步中自定义的设备功能。

1. 选择 `Is greater than` 作为“运算符”  。 

1. 输入环境的典型范围上限室内湿度级别作为“值”  。 例如，输入“65”  。 已为在任何 RuuviTag 实际或模拟传感器中的相对湿度超出此值时发生的规则设置条件。 可能需要根据环境中的正常湿度范围向上或向下调整值。  

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-conditions.png" alt-text="Azure IoT Central 添加规则条件。":::

若要向规则添加操作，请执行以下操作：

1. 选择“+ 电子邮件”  。

1. 输入“高湿度通知”  作为操作的友好显示名称  。 

1. 在“收件人”中输入与帐户关联的电子邮件地址  。 如果使用其他电子邮件，则所使用的地址必须是已添加到应用程序的用户的地址。 该用户还需要至少进行一次登录和注销。

1. （可选）输入要包含在电子邮件文本中的注释。

1. 选择“完成”  ，完成操作。

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-action.png" alt-text="Azure IoT Central 向规则添加操作。":::

1. 选择“保存”  以保存并激活新规则。 

    几分钟后，指定的电子邮件帐户应开始收到电子邮件。 每当传感器指示湿度级别超出条件中的值时，应用程序都会发送电子邮件。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

* 使用 Azure IoT Central“店内分析 - 结帐”模板来创建零售商店应用程序 
* 自定义应用程序设置
* 创建和自定义 IoT 设备模板
* 将设备连接到应用程序
* 添加规则和操作来监视条件

请注意，在创建 Azure IoT Central 条件监视应用程序之后，建议接下来执行以下后续步骤：

> [!div class="nextstepaction"]
> [自定义仪表板](./tutorial-in-store-analytics-customize-dashboard.md)
