---
title: 教程 - 在 Azure IoT Central 应用程序中使用设备组 | Microsoft Docs
description: 教程 - 了解如何在 Azure IoT Central 应用程序中使用设备组分析来自设备的遥测数据。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da7c1c0268f04b183ba48491bd5f0d0b01e15b41
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224090"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>教程：使用设备组分析设备遥测数据

本文介绍如何在 Azure IoT Central 应用程序中使用设备组分析设备遥测数据。

设备组是由于与某个指定的条件匹配，而分组到一起的一系列设备。 设备组通过将设备分组到较小的逻辑组，来帮助大规模管理、可视化和分析设备。 例如，可以针对西雅图的所有空调设备创建一个设备组，使技术人员能够找到他们所要负责的设备。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建设备组
> * 使用设备组分析设备遥测数据

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
    | 最后维修日期 | 无          | 日期   |
    | 客户名称     | 无          | 字符串 |

1. 选择“保存”  以保存更改。

向设备模板添加新的窗体以管理设备：

1. 选择“视图”节点，然后选择“编辑设备和云数据”磁贴来添加新视图。

1. 将窗体名称更改为“管理设备”。

1. 依次选择“客户名称”、“上次服务日期”云属性和“目标温度”属性  。 然后选择“添加部分”。

1. 选择“保存”以保存新窗体。

现在发布设备模板。

## <a name="create-simulated-devices"></a>创建模拟设备

创建设备组之前，请根据要在本教程中使用的传感器控制器设备模板添加至少五个模拟设备：

:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="显示 5 个模拟传感器控制器设备的屏幕截图":::

对于其中 4 台模拟传感器设备，请使用“管理设备”视图将客户名称设为“Contoso”，然后选择“保存”。

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="显示如何设置“客户名称”云属性的屏幕截图":::

## <a name="create-a-device-group"></a>创建设备组

1. 选择左侧窗格中的“设备组”，导航到设备组页面。

1. 选择“+ 新建”  。

1. 将设备组命名为“Contoso 设备”。 还可以添加说明。 设备组只能包含单个设备模板中的设备。 选择要用于此组的传感器控制器设备模板。

1. 若要自定义设备组以仅包括属于“Contoso”  的设备，请选择“+ 筛选器”  。 选择“客户名称”  属性、“等于”  比较运算符以及“Contoso”  作为值。 你可以添加多个筛选器，并且满足所有  筛选条件的设备将放置在设备组中。 有权访问该应用程序的任何人都可以访问创建的设备组，因此，任何人都可以查看、修改或删除该设备组。

    > [!TIP]
    > 设备组是动态查询。 每当查看设备列表时，列表中都可能显示不同的设备。 列表内容取决于哪些设备当前满足查询的条件。

1. 选择“保存”  。

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="显示设备组查询配置的屏幕截图":::

> [!NOTE]
> 对于 Azure IoT Edge 设备，请选择用于创建设备组的 Azure IoT Edge 模板。

## <a name="analytics"></a>分析

可以结合使用 **Analytics** 和设备组来分析该组中的设备发来的遥测数据。 例如，可以绘制所有 Contoso 环境传感器报告的平均温度的图形。

若要分析设备组的遥测数据：

1. 在左侧窗格中选择“Analytics”。 

1. 选择创建的“Contoso 设备”设备组。  然后，添加“温度”和“湿度”遥测类型：  

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="显示所选的用于分析的遥测类型的屏幕截图":::

    使用遥测类型旁边的省略号图标选择聚合类型。 默认类型为“平均”。  使用“分组依据”更改聚合数据的显示方式。 例如，如果按设备 ID 进行拆分，则选择“分析”时，将看到每个设备的绘图。 

1. 选择“分析”以查看平均遥测值： 

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="显示所有 Contoso 设备平均值的屏幕截图":::

    你可以自定义视图、更改显示的时间段，以及将数据导出为 CSV 或以表格形式查看数据。

    :::image type="content" source="media/tutorial-use-device-groups/export-data.png" alt-text="屏幕截图：显示如何导出 Contoso 设备数据":::

若要详细了解分析功能，请参阅[如何使用分析功能来分析设备数据](howto-create-analytics.md)。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备组后，建议接下来学习：

> [!div class="nextstepaction"]
> [如何创建遥测规则](tutorial-create-telemetry-rules.md)
