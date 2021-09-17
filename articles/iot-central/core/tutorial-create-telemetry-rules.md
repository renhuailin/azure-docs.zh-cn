---
title: 教程 - 在 Azure IoT Central 应用程序中创建和管理规则
description: 本教程介绍如何通过 Azure IoT Central 规则以近实时方式监视设备并自动调用操作（例如在触发规则时发送电子邮件）。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6a9a7d23d4fb8f11c27f279bdef8d2b46274b21e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743394"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>教程：在 Azure IoT Central 应用程序中创建规则并设置通知

可以使用 Azure IoT Central 对连接的设备进行远程监视。 使用 Azure IoT Central 规则，可以近乎实时地监视设备并自动调用操作（例如，发送电子邮件）。 本文介绍如何创建规则来监视设备发送的遥测数据。

设备使用遥测从设备发送数值数据。 选定的遥测超过指定阈值时会触发规则。

在本教程中，你将创建一个规则，指示在模拟传感器设备中的温度超过 70&deg; F 时发送电子邮件。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建规则
> * 添加电子邮件操作

## <a name="prerequisites"></a>先决条件

完成本教程中的步骤需要具备以下各项：

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="add-and-customize-a-device-template"></a>添加并自定义设备模板

从设备目录添加设备模板。 本教程使用“ESP32-Azure IoT 工具包”设备模板：

1. 若要添加新的设备模板，请在“设备模板”页上选择“+ 新建” 。

1. 在“选择类型”页上，向下滚动直到在“使用预配置的设备模板”部分中找到“ESP32-Azure IoT 套件”磁贴。  

1. 选择“ESP32-Azure IoT 工具包”磁贴，然后选择“下一页: 查看”。

1. 在“查看”页上，选择“创建”   。

创建的模板的名称为“传感器控制器”。 该模型包含“传感器控制器”、“SensorTemp”和“设备信息接口”等组件  。 组件定义 ESP32 设备的功能。 功能包括遥测、属性和命令。

将两个云属性添加到“传感器控制器”设备模板：

1. 依次选择“云属性”、“+ 添加云属性”。 使用下表中的信息将两个云属性添加到设备模板：

    | 显示名称      | 语义类型 | 架构 |
    | ----------------- | ------------- | ------ |
    | 最后维修日期 | 无          | Date   |
    | 客户名称     | 无          | 字符串 |

1. 选择“保存”  以保存更改。

向设备模板添加新的窗体以管理设备：

1. 选择“视图”节点，然后选择“编辑设备和云数据”磁贴来添加新视图。

1. 将窗体名称更改为“管理设备”。

1. 依次选择“客户名称”、“上次服务日期”云属性和“目标温度”属性  。 然后选择“添加部分”。

1. 选择“保存”以保存新窗体。

现在发布设备模板。

## <a name="create-a-rule"></a>创建规则

若要创建遥测规则，设备模板必须至少包含一个遥测值。 本教程使用模拟的传感器控制器设备来发送温度和湿度遥测数据。 该规则监视设备报告的温度，并在温度超过 70 度时发送电子邮件。

> [!NOTE]
> 每个应用程序限制 50 条规则。

1. 在左窗格中，选择“规则”  。

1. 如果尚未创建任何规则，则会看到以下屏幕：

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="显示空白规则列表的屏幕截图":::

1. 选择“+新建”以添加新规则。

1. 输入名称“温度监视器”以标识规则，然后按 Enter  。

1. 选择“传感器控制器”设备模板。 默认情况下，此规则自动应用到与此设备模板关联的所有设备。 要筛选设备的子集，请选择“+ 筛选器”，然后使用设备属性来标识设备  。 要禁用规则，请切换“启用/禁用”按钮：

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="显示在规则定义中选择设备模板的屏幕截图":::

### <a name="configure-the-rule-conditions"></a>配置规则条件

条件可定义规则监视的条件。 在本教程中，将配置在温度超过 70&deg; F 时触发的规则。

1. 在“遥测”下拉列表中选择“温度”   。

1. 接下来，选择“大于”作为“运算符”，并输入“70”作为“值”     。

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="显示规则的温度条件的屏幕截图":::

1. （可选）可以设置“时间聚合”  。 选择时间聚合时，还必须从聚合下拉列表中选择一种聚合类型（例如，平均值或求和）。

    * 如无聚合，此规则将对每个满足此条件的遥测数据点触发。 例如，如果将规则配置为在温度高于 70 度时触发，则当设备温度超过此值时，该规则会几乎立即触发。
    * 使用聚合时，如果时间窗口中遥测数据点的聚合值满足条件，将触发规则。 例如，如果将规则配置为在温度高于 70 时触发，且平均时间聚合为 10 分钟，则当设备报告在过去 10 分钟间隔计算得出的平均温度超过 70 度时，将触发该规则。

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="显示填写了聚合条件的屏幕截图":::

选择“+ 条件”，将多个条件添加到规则中  。 添加多个条件时，可以指定是在必须满足所有条件还是任一条件时触发规则。 如果在多个条件下使用时间聚合，则必须聚合所有遥测值。

### <a name="configure-actions"></a>配置操作

定义条件后，可以设置触发规则时要执行的操作。 此规则中指定的所有条件评估结果均为 true 时会调用操作。

1. 在“操作”部分中选择“+ 电子邮件”   。

1. 输入“温度警告”作为操作的显示名称，在“收件人”字段中输入电子邮件地址，然后输入“你应检查设备!”    作为显示在电子邮件正文中的注释。

    > [!NOTE]
    > 电子邮件只发送给那些已添加到应用程序中并已至少登录一次的用户。 详细了解 Azure IoT Central 中的[用户管理](howto-administer.md)。

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="显示规则的电子邮件操作的屏幕截图":::

1. 要保存操作，请选择“完成”  。 可以向规则添加多个操作。

1. 若要保存规则，请选择“保存”。  此规则在数分钟内即可生效，然后开始监视发送到应用程序的遥测数据。 满足规则中指定的条件时，规则会触发配置的电子邮件操作。

一段时间后，规则触发时，你将收到一封电子邮件：

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="显示通知电子邮件的屏幕截图":::

## <a name="delete-a-rule"></a>删除规则

如果不再需要某项规则，可打开该规则并选择“删除”，以将其删除。 

## <a name="enable-or-disable-a-rule"></a>启用或禁用规则

选择要启用或禁用的规则。 切换规则中的“启用/禁用”按钮，为该规则范围内的所有设备启用或禁用该规则  。

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>对特定的设备启用或禁用规则

选择要自定义的规则。 在“目标设备”部分使用一个或多个筛选器，将规则范围缩小到要监视的设备  。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

* 创建基于遥测的规则
* 添加操作

现在，你已定义了基于阈值的规则，建议接下来学习如何：

> [!div class="nextstepaction"]
> [配置规则](howto-configure-rules.md)
