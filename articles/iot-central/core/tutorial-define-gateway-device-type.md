---
title: 教程 - 在 Azure IoT Central 中定义新的网关设备类型 | Microsoft Docs
description: 本教程向构建人员介绍如何在 Azure IoT Central 应用程序中定义新的 IoT 网关设备类型。
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 75b818382102642ecc8380b257c9c31382b8283d
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588410"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>教程 - 在 Azure IoT Central 应用程序中定义新的 IoT 网关设备类型

本教程介绍了如何使用网关设备模板在 IoT Central 应用程序中定义网关设备。 然后配置多个下游设备，这些设备通过网关设备连接到 IoT Central 应用程序。 

在本教程中，你将创建一个“智能建筑物”网关设备模板。  “智能建筑物”网关设备与其他下游设备发生关系。 

![网关设备与下游设备之间的关系示意图](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

除了使下游设备能够与 IoT Central 应用程序通信以外，网关设备还可以：

* 发送自身的遥测数据，例如温度。
* 响应操作员做出的可写属性更新。 例如，操作员可能会更改遥测数据的发送间隔。
* 响应命令，例如重新启动设备。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 创建下游设备模板
> * 创建网关设备模板
> * 发布设备模板
> * 创建模拟设备

## <a name="prerequisites"></a>先决条件

完成本教程中的步骤需要具备以下各项：

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="create-downstream-device-templates"></a>创建下游设备模板

本教程使用“S1 传感器”设备和“RS40 占位传感器”设备的设备模板来生成模拟的下游设备。  

若要创建“S1 传感器”设备的设备模板： 

1. 在左窗格中选择“设备模板”  。 然后选择“+新建”，开始添加模板。

1. 向下滚动，直到看到“Minew S1”设备的磁贴。 选择该磁贴，然后选择“下一步:  自定义”。

1. 在“复查”页上，选择“创建”将该设备模板添加到应用程序。   

若要创建“RS40 占位传感器”设备的设备模板：

1. 在左窗格中选择“设备模板”  。 然后选择“+新建”，开始添加模板。

1. 向下滚动，直到看到“RS40 占位传感器”设备的磁贴。  选择该磁贴，然后选择“下一步:  自定义”。

1. 在“复查”页上，选择“创建”将该设备模板添加到应用程序。   

现已创建两个下游设备类型的设备模板：

![下游设备的设备模板](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>创建网关设备模板

在本教程中，你将从头开始创建网关设备的设备模板。 稍后将使用此模板在应用程序中创建模拟的网关设备。

若要将新的网关设备模板添加到应用程序：

1. 在左窗格中选择“设备模板”  。 然后选择“+新建”，开始添加模板。

1. 在“选择模板类型”页上选择“IoT 设备”磁贴，然后选择“下一步:    自定义”。

1. 在“自定义设备”页面上，选中“这是网关设备”复选框 。

1. 输入“智能建筑物网关设备”作为模板名称，然后选择“下一步: 查看” 。

1. 在“查看”页上，选择“创建”   。 



1. 在“创建模型”页面上，选择“自定义模型”磁贴 。

1. 选择“+添加功能”，添加一个功能。

1. 输入“发送数据”作为显示名称，然后选择“属性”作为功能类型 。

1. 选择“+添加功能”，再添加一个功能。 输入“布尔遥测”作为显示名称，选择“遥测”作为功能类型，然后选择“布尔”作为架构  。

1. 选择“保存”。

### <a name="add-relationships"></a>添加关系

接下来，将关系添加到下游设备模板中：

1. 在“智能建筑物网关设备”模板中，选择“关系”。  

1. 选择“+ 添加关系”。  输入“环境传感器”作为显示名称，选择“S1 传感器”作为目标。  

1. 再次选择“+ 添加关系”。  输入“占位传感器”作为显示名称，选择“RS40 占位传感器”作为目标。  

1. 选择“保存”。 

![智能建筑物网关设备模板，其中显示了关系](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>添加云属性

网关设备模板可以包含云属性。 云属性仅存在于 IoT Central 应用程序中，永远不会与设备相互发送和接收信息。

若要将云属性添加到“智能建筑物网关设备”模板： 

1. 在“智能建筑物网关设备”模板中，选择“云属性”。  

1. 使用下表中的信息将两个云属性添加到网关设备模板。

    | 显示名称      | 语义类型 | 架构 |
    | ----------------- | ------------- | ------ |
    | 最后维修日期 | 无          | 日期   |
    | 客户名称     | 无          | 字符串 |

1. 选择“保存”。 

### <a name="create-views"></a>创建视图

构建人员可将应用程序自定义为向操作员显示有关环境传感器设备的信息。 完成自定义后，操作员可以管理连接到应用程序的环境传感器设备。 可为操作员创建两种类型的视图来让他们与设备交互：

* 用于查看和编辑设备与云属性的窗体。
* 用于可视化设备的视图。

若要为“智能建筑物网关设备”模板生成默认视图： 

1. 在“智能建筑物网关设备”模板中，选择“视图”。  

1. 选择“生成默认视图”磁贴，并确保选择所有选项。 

1. 选择“生成默认仪表板视图”。 

## <a name="publish-the-device-template"></a>发布设备模板

需要先发布设备模板，然后才能创建模拟的网关设备或连接真实的网关设备。

若要发布网关设备模板：

1. 在“设备模板”页中选择“智能建筑物网关设备”模板。  

2. 选择“发布”  。

3. 在“发布设备模板”对话框中，选择“发布”   。

发布设备模板后，它将显示在“设备”页上，操作员可以看到它。  操作员可以使用模板创建设备实例或建立规则和监视。 编辑已发布的模板可能会影响整个应用程序中的行为。

若要详细了解在发布设备模板后如何对其进行修改，请参阅[编辑现有设备模板](howto-edit-device-template.md)。

## <a name="create-the-simulated-devices"></a>创建模拟设备

本教程使用模拟的下游设备和模拟的网关设备。

若要创建模拟的网关设备：

1. 在“设备”页上的设备模板列表中，选择“智能建筑物网关设备”。  

1. 选择“+新建”，开始添加新设备。

1. 保留生成的“设备 ID”和“设备名称”。   确保“模拟”切换开关为“打开”。   选择“创建”  。

若要创建模拟的下游设备：

1. 在“设备”页上的设备模板列表中，选择“RS40 占位传感器”。  

1. 选择“+新建”，开始添加新设备。

1. 保留生成的“设备 ID”和“设备名称”。   确保“模拟”切换开关为“打开”。   选择“创建”  。

1. 在“设备”页上的设备模板列表中，选择“S1 传感器”。  

1. 选择“+新建”，开始添加新设备。

1. 保留生成的“设备 ID”和“设备名称”。   确保“模拟”切换开关为“打开”。   选择“创建”  。

![应用程序中的模拟设备](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>将下游设备关系添加到网关设备

在应用程序中创建模拟设备后，接下来可以在下游设备与网关设备之间创建关系：

1. 在“设备”页上的设备模板列表中选择“S1 传感器”，然后选择模拟的“S1 传感器”设备。   

1. 选择“附加到网关”。

1. 在“附加到网关”对话框中，选择“智能建筑物网关设备”模板，然后选择之前创建的模拟实例 。

1. 选择“附加”。

1. 在“设备”页上的设备模板列表中选择“RS40 占位传感器”，然后选择模拟的“RS40 占位传感器”设备。   

1. 选择“连接到网关”。 

1. 在“连接到网关”对话框中选择“智能建筑物网关设备”模板，然后选择前面创建的模拟实例。  

1. 选择“附加”。

这两个模拟的下游设备现在都已连接到模拟的网关设备。 如果导航到网关设备的“下游设备”视图，可以看到相关的下游设备： 

![下游设备视图](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

## <a name="connect-real-downstream-devices"></a>连接真实的下游设备

在[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)教程中，示例代码将演示如何在设备发送的预配有效负载中包含设备模板中的模型 ID。 模型 ID 允许 IoT Central 将设备与正确的设备模板关联。 例如：

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
  provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
    provisioning_host=provisioning_host,
    registration_id=registration_id,
    id_scope=id_scope,
    symmetric_key=symmetric_key,
  )

  provisioning_device_client.provisioning_payload = {"modelId": model_id}
  return await provisioning_device_client.register()
```

连接下游设备时，你可以修改预配有效负载，使其包含网关设备的 ID。 模型 ID 允许 IoT Central 将设备与正确的下游设备模板关联。 网关 ID 允许 IoT Central 在下游设备及其网关之间建立关系。 在这种情况下，设备发送的预配有效负载类似于以下 JSON：

```json
{
  "modelId": "dtmi:rigado:S1Sensor;2",
  "iotcGateway":{
    "iotcGatewayId": "gateway-device-001"
  }
}
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

* 创建新的 IoT 网关作为设备模板。
* 创建云属性。
* 创建自定义项。
* 定义设备遥测的可视化效果。
* 添加关系。
* 发布设备模板。

接下来，你可以学习如何执行以下操作：

> [!div class="nextstepaction"]
> [将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序](tutorial-add-edge-as-leaf-device.md)
