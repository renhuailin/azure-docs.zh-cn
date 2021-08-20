---
title: 快速入门 - 在 Azure IoT Central 中配置规则和操作
description: 本快速入门演示如何在 IoT Central 应用程序中配置基于遥测的规则和操作。
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 9357198e140a6ba403fcb74787d31a0940554fa7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459120"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>快速入门：在 Azure IoT Central 中配置用于设备的规则和操作

在本快速入门中，你将创建一个 IoT Central 规则，该规则在有人翻转你的手机时向你发送一封电子邮件。

## <a name="prerequisites"></a>必备条件

开始之前，应完成之前的快速入门 [创建和使用 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)以将 **IoT 即插即用** 智能应用连接到你的 IoT Central 应用程序。

## <a name="create-a-telemetry-based-rule"></a>创建基于遥测的规则

智能应用发送遥测数据，其中包括来自加速计传感器的值。 手机背面平放时，z 值大于 `9`，手机正面平放时，z 值小于 `-9`。 

1. 若要为应用程序添加新的基于遥测的规则，请在左侧窗格中选择“规则”  。

1. 若要创建新规则，请选择“创建规则”。

1. 输入“手机被翻转”作为规则名称。

1. 在“目标设备”部分中，选择“IoT 即插即用移动设备”作为设备模板。   此选项根据设备模板类型筛选规则适用于的设备。 可以通过选择“+ 筛选器”  添加更多筛选条件。

1. 在“条件”  部分，定义触发规则的条件。 使用以下信息基于加速计 z 轴遥测定义单个条件。 此规则使用聚合，对于每台设备，你在 5 分钟间隔内最多收到一封电子邮件：

    | 字段            | 值            |
    |------------------|------------------|
    | 时间聚合 | 开启, 5 分钟    |
    | 遥测        | 加速 / Z |
    | 运算符         | 小于     |
    | 聚合      | 最小值          |
    | 值            | -9               |

    :::image type="content" source="media/quick-configure-rules/rule-target-condition.png" alt-text="显示规则条件的屏幕截图。":::

1. 若要添加要在规则触发时运行的电子邮件操作，请选择“操作”部分中的“+ 电子邮件” 。

1. 使用下表中的信息定义操作，然后选择“完成”  ：

    | 设置      | 值                    |
    |--------------|--------------------------|
    | 显示名称 | 手机已移动         |
    | 目标           | 你的电子邮件地址       |
    | 说明        | 手机已正面朝下！ |

    > [!TIP]
    > 若要接收电子邮件通知，电子邮件地址必须是[应用程序中的用户 ID](howto-manage-users-roles.md)，并且该用户必须至少登录过应用程序一次。

    :::image type="content" source="media/quick-configure-rules/rule-action.png" alt-text="显示添加到规则的电子邮件操作的屏幕截图":::

1. 选择“保存”。  你的规则现在“规则”页面上列出。

## <a name="test-the-rule"></a>测试规则

保存规则后不久，它将变为活动状态。 满足规则中定义的条件时，IoT Central 应用程序会将电子邮件发送到操作中指定的地址。

若要触发规则，请确保智能手机应用正在发送数据，然后将手机正面朝下放在桌面上。 应用现在发送加速计 z 轴遥测值小于 `-9` 的消息。 5 分钟后，IoT Central 会发送一封电子邮件，通知你的智能手机已正面朝下。

测试完成后，禁用该规则以停止在收件箱中接收通知电子邮件。

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 创建基于遥测的规则
* 添加操作

若要详细了解如何将 IoT Central应用程序与其他服务集成，请参阅：

> [!div class="nextstepaction"]
> [从 IoT Central 应用程序导出和处理数据](quick-export-data.md)。
