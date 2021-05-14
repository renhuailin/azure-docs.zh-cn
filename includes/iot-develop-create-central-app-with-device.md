---
title: include 文件
description: include 文件
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 981ebfece7bc12584adfdf1e565bd275d8e1b2fb
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226644"
---
## <a name="create-an-application"></a>创建应用程序
可以通过多种方式将设备连接到 Azure IoT。 本部分介绍如何使用 Azure IoT Central 连接设备。 IoT Central 是一个 IoT 应用程序平台，可降低管理 IoT 解决方案中的设备的成本和复杂性。

要创建新的应用程序：
1. 浏览到 [Azure IoT Central](https://apps.azureiotcentral.com/)，并使用 Microsoft 个人帐户、工作帐户或学校帐户登录。
1. 导航到“生成”，然后选择“自定义应用”。 
   :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-build.png" alt-text="IoT Central 起始页":::
1. 在“应用程序名称”中，输入唯一的名称或使用已生成的名称。
1. 在“URL”中，输入易记的应用程序 URL 前缀，或使用已生成的 URL 前缀。
1. 将“应用程序模板”保持设置为“自定义应用程序”。 
1. 选择一个“定价计划”选项。 
    - 若要免费使用该应用程序 7 天，请选择“免费”。 在免费的应用程序过期之前，可将其转换为标准定价。
    - 或者，可以选择标准定价计划。 如果选择标准定价，将会显示更多选项，并且你将需要设置“目录”、“Azure 订阅”和“位置”。   若要了解定价，请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 
        - “目录”是将在其中创建应用程序的 Azure Active Directory  。 Azure Active Directory 包含用户标识、凭据和其他组织信息。 如果你没有 Azure Active Directory，则在你创建 Azure 订阅时，系统会创建一个。
        - 使用 **Azure 订阅** 可以创建 Azure 服务的实例。 IoT Central 将在订阅中预配资源。 如果你没有订阅，可[免费](https://aka.ms/createazuresubscription)创建一个。 如果你有订阅，可在下拉列表中选择它。
        - “位置”是要在其中创建应用程序的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 请选择实际上距离设备最近的位置以获得最佳性能。 选择一个位置后，无法将应用程序转移到其他位置。

    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-pricing.png" alt-text="IoT Central -“新建应用程序”对话框":::
1. 选择“创建”。
    
    IoT Central 在创建应用程序后，会将你重定向到应用程序仪表板。
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-created.png" alt-text="IoT Central -“新建应用程序”仪表板":::

## <a name="add-a-device"></a>添加设备
在本部分，你要将新设备添加到 IoT Central 应用程序。 该设备是设备模板的实例，表示要连接到应用程序的真实设备或模拟设备。 

若要创建新设备，请执行以下操作：
1. 在左窗格中，依次选择“设备”、“+新建”。 
1. 将“设备模板”保持设置为“未分配”。

    > [!NOTE]
    > 为简单起见，在本快速入门中，你将连接一个使用未分配的模板的模拟设备。 如果你一直使用 IoT Central 来管理设备，那么你将会了解如何使用设备模板。 有关使用设备模板的概述，请参阅[快速入门：将模拟设备添加到 IoT Central 应用程序](../articles/iot-central/core/quick-create-simulated-device.md)。
1. 设置易记的“设备名称”和“设备 ID”。  或者，也可以使用生成的值。
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-create-device.png" alt-text="IoT Central -“新建设备”对话框":::
1. 选择“创建”。

    创建的设备显示在“所有设备”列表中。
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-devices-list.png" alt-text="IoT Central -“所有设备”列表":::
    
若要获取新设备的连接详细信息：
1. 在“所有设备”列表中，单击链接设备的名称以显示详细信息。 
1. 在顶部菜单中，选择“连接”。

    “设备连接”对话框显示了连接详细信息：:::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-device-connect.png" alt-text="IoT Central 设备连接详细信息":::
1. 将“设备连接”对话框中的以下值复制到安全位置。 你要使用这些值将设备连接到 IoT Central。
    * `ID scope`
    * `Device ID`
    * `Primary key`