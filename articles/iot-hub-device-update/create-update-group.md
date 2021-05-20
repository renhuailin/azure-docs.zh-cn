---
title: 在 Azure IoT 中心设备更新中创建设备组 | Microsoft Docs
description: 在 Azure IoT 中心设备更新中创建设备组
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679103"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>在 IoT 中心设备更新中创建设备组
IoT 中心设备更新允许将更新部署到一组 IoT 设备。

## <a name="prerequisites"></a>先决条件

* [访问启用了 IoT 中心设备更新的 IoT 中心](create-device-update-account.md)。 建议为 IoT 中心使用 S1（标准）层或更高版本。 
* 为 IoT 中心内的设备更新预配的 IoT 设备（或模拟器）。
* [已为预配设备成功导入了至少一个更新](import-update.md)。

## <a name="add-a-tag-to-your-devices"></a>将标记添加到设备  

IoT 中心设备更新允许将更新部署到一组 IoT 设备。 若要创建组，第一步是将标记添加到 IoT 中心中的目标设备集。 只有将标记连接到设备更新后，才能将其成功添加到设备。

以下文档介绍了如何添加和更新标记。

### <a name="programmatically-update-device-twin"></a>以编程方式更新设备孪生

在设备更新中注册设备后，可以使用 RegistryManager 通过相应的标记更新设备孪生。 
[了解如何使用示例 .NET 应用添加标记](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)。  
[了解标记属性](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format)。

#### <a name="device-update-tag-format"></a>设备更新标记格式

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>使用作业

可以按照以下[这些](../iot-hub/iot-hub-devguide-jobs.md)示例在多个设备上计划作业，以添加或更新设备更新标记。 [了解详细信息](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md)。

  > [!NOTE] 
  > 此操作违反了当前的 IOT 中心消息配额，建议一次最多只能更改 50,000 个设备孪生标记，否则，如果超出每日的 IoT 中心消息配额，则可能需要购买更多 IoT 中心单位。 详细信息请见[配额和限制](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)。

### <a name="direct-twin-updates"></a>直接孪生更新

还可以直接在设备孪生中添加或更新标记。

1. 登录到 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。

2. 从左侧导航窗格上的“IoT 设备”或“IoT Edge”中，找到 IoT 设备并导航到“设备孪生”。

3. 在“设备孪生”中，通过将任何现有的 Device Update 标记值设置为 null 来将其删除。

4. 按如下所示添加新的 Device Update 标记值。 [使用标记的示例设备孪生 JSON 文档](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>限制

* 你可以将任何值添加到标记中，为保留值的“未归类”除外。
* 标记值的长度不得超过 255 个字符。
* 标记值可以包含字母数字字符和以下特殊字符：“.”、“,”、“-”、“_”、“〜”。
* 标记和组名称区分大小写。
* 设备只能有一个名称为 ADUGroup 的标记，后续添加的以该名称命名的标记都会覆盖标记名 ADUGroup 的现有值。
* 一个设备只能属于一个组。

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>通过选择现有的 IoT 中心标记来创建设备组

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 选择你之前连接到 Device Update 实例的 IoT 中心。

3. 从左侧导航栏中选择“自动设备管理”下的“设备更新”选项。

4. 选择页面顶部的“组”选项卡。 你将能够看到连接到尚未分组的设备更新的设备数。
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="未分组设备的屏幕截图。" lightbox="media/create-update-group/ungrouped-devices.png":::

5. 选择“添加”按钮以创建新组。
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="添加设备组的屏幕截图。" lightbox="media/create-update-group/add-group.png":::

6. 从列表中选择一个 IoT 中心标记，然后选择“创建更新组”。
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="选择标记的屏幕截图。" lightbox="media/create-update-group/select-tag.png":::

7. 创建组后，会看到更新符合性图表和组列表已更新。  更新符合性图表显示了处于各种符合性状态的设备计数：最新的更新、可用的新更新、正在进行的更新和尚未分组的设备。 [了解更新符合性](device-update-compliance.md)。
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="更新符合性视图的屏幕截图。" lightbox="media/create-update-group/updated-view.png":::

8. 你应看到新创建的组和新组中设备的任何可用更新。 你可以通过单击更新名称，从此视图中将更新部署到新组。 有关更多详细信息，请参阅“下一步：部署更新”。

## <a name="view-device-details-for-the-group-you-created"></a>查看创建的组中的设备详细信息

1. 导航到新创建的组，然后单击组名称。

2. 属于该组的设备的列表将连同其设备更新属性一起显示。 在此视图中，还可以查看作为组成员的所有设备的更新符合性信息。 更新符合性图表显示了处于各种符合性状态的设备计数：最新的更新、可用的新更新和正在进行的更新。
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="设备组详细信息视图的屏幕截图。" lightbox="media/create-update-group/group-details.png":::

3. 你还可以单击要重定向到 IoT 中心中的设备详细信息页的组中的每个设备。
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="设备详细信息视图的屏幕截图。" lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>后续步骤 

[部署更新](deploy-update.md)

[详细了解设备组](device-update-groups.md)

[了解更新符合性](device-update-compliance.md)。
