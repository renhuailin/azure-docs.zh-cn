---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 76f8c8819bf67405023980a6fd0e48f997926938
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111896435"
---
<!-- All needs updating -->
Azure IoT Central 应用程序中的操作员可以：

* 在“概述”页上查看两个恒温组件发送的遥测数据：

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="查看设备遥测数据":::

* 在“关于”页上查看设备属性。 此页显示设备信息组件和两个恒温器组件的属性：

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="查看设备属性":::

## <a name="customize-the-device-template"></a>自定义设备模板

作为解决方案开发人员，你可以对 IoT Central 在温度控制器设备连接时自动创建的设备模板进行自定义。

添加云属性以存储与设备关联的客户名称：

1. 在 IoT Central 应用程序中，导航到“设备模板”页面上的“温度控制器”设备模板 。

1. 在“温度控制器”设备模板中，选择“云属性”。 

1. 选择“添加云属性”。 输入自定义名称作为“显示名称”，展开项，并选择“字符串”作为“架构”  。 再选择“保存”。

若要自定义“获取最高-最低温度报告”命令在 IoT Central 应用程序中的显示方式，请按照以下说明操作：

1. 在设备模板中，选择“自定义”。

1. 对于“getMaxMinReport (thermostat1)”，请将“获取最高-最低温度报告” 替换为“获取恒温器 1 状态报告”。

1. 对于“getMaxMinReport (thermostat2)”，请将“获取最高-最低温度报告” 替换为“获取恒温器 2 状态报告”。

1. 选择“保存”。

若要自定义“目标温度”可写属性在 IoT Central 应用程序中的显示方式：

1. 在设备模板中，选择“自定义”。

1. 对于“targetTemperature (thermostat1)”，请将“目标温度”替换为“目标温度 (1)”。 

1. 对于“targetTemperature (thermostat2)”，请将“目标温度”替换为“目标温度 (2)”。 

1. 选择“保存”。

“温度控制器”模型中的恒温器组件包含“目标温度”可写属性，设备模板包含“客户名称”云属性。 操作员可以使用“创建视图”来编辑以下属性：

1. 选择“视图”，然后选择“编辑设备和云数据”磁贴。

1. 输入“属性”作为窗体名称。

1. 选择“目标温度 (1)”、“目标温度 (2)”和“客户名称”属性。   然后选择“添加部分”。

1. 保存所做更改。

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="正在更新属性值的视图":::

## <a name="publish-the-device-template"></a>发布设备模板

必须先发布设备模板，操作员才能查看和使用你进行的自定义。

从“恒温器”设备模板中，选择“发布”。  在“将此设备模板发布到应用程序”面板中，选择“发布”。

操作员现在可以使用“属性”视图来更新属性值，并在设备命令页上调用“获取恒温器 1 状态报告”和“获取恒温器 2 状态报告”的命令：  

* 更新“属性”页上的可写属性值：

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="更新设备属性":::

* 从“命令”页中调用命令。 如果运行状态报告命令，请在运行该命令之前为参数“自”选择一个日期和时间：

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="调用命令":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="查看命令响应":::
