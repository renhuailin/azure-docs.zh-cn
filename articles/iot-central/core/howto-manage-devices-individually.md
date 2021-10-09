---
title: 在 Azure IoT Central 应用程序中单独管理设备 | Microsoft Docs
description: 了解如何在 Azure IoT Central 应用程序中单独管理设备。 创建、删除和更新设备。
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0ca2190bf5994edec56dce638d7361d90d2da2de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810672"
---
# <a name="manage-individual-devices-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中管理单个设备

本文介绍如何在 Azure IoT Central 应用程序中管理设备。 可以执行以下操作：

- 使用“设备”页查看、添加和删除与 Azure IoT Central 应用程序连接的设备。
- 通过从视图中更改存储在设备属性中的值，使你的设备元数据保持最新。
- 通过在视图中更新特定设备的设置，来控制设备的行为。

要了解如何管理自定义的设备组，请参阅[教程：使用设备组分析设备遥测](tutorial-use-device-groups.md)。

## <a name="view-your-devices"></a>查看设备

若要查看单个设备：

1. 在左侧窗格中选择“设备”。 此处显示了设备模板的列表，以及你的组织可以访问的所有设备的列表。

1. 选择一个设备模板。

1. 在“设备”页面的右侧窗格中，你会看到根据该设备模板创建的你的组织可以访问的设备的列表。 选择单个设备以查看该设备的“设备详细信息”页：

    :::image type="content" source="media/howto-manage-devices-individually/device-list.png" alt-text="显示设备列表的屏幕截图。":::

    > [!TIP]
    > 可使用此页上的筛选器工具来查看某个特定组织中的设备。

## <a name="add-a-device"></a>添加设备

若要将设备添加到 Azure IoT Central 应用程序：

1. 在左侧窗格中选择“设备”。

1. 选择要从中创建设备的设备模板。

1. 选择“+ 新建”。

1. 输入设备名称和 ID，或接受默认值。 设备名称最长 148 个字符。 设备 ID 最长 128 个字符。

1. 将“模拟”开关切换为“打开”或“关闭”。 真实设备是指要连接到 Azure IoT Central 应用程序的物理设备。 模拟设备包含 Azure IoT Central 生成的示例数据。

1. 如果应用程序使用组织，请选择该设备所属的组织。

    > [!TIP]
    > 可以设置要显示在组织下拉列表中的[默认组织](howto-create-organizations.md#default-organization)。

1. 选择“创建”  。

1. 此设备随即显示在此模板的设备列表中。 选择设备查看设备详细信息页，其中包含该设备的所有视图。

## <a name="change-organization"></a>更改组织

若要将设备移动到不同组织，对于源组织和目标组织都必须有访问权限。 若要移动设备，请执行以下操作：

1. 在左侧窗格中选择“设备”。

1. 在设备列表中选择要移动的设备。

1. 选择“组织”。

1. 为该设备选择新组织：

    :::image type="content" source="media/howto-manage-devices-individually/change-device-organization.png" alt-text="显示如何将设备移动到新组织的屏幕截图。":::

1. 选择“保存”。

## <a name="migrate-devices-to-a-template"></a>将设备迁移到模板

如果通过在“所有设备”下启动导入来注册设备，则无需任何设备模板关联即可创建设备。 设备必须与模板关联起来，才能浏览有关设备的数据和其他详细信息。 请按照以下步骤将设备与模板进行关联：

1. 在左侧窗格中选择“设备”。

1. 在左侧面板中选择“所有设备”：

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-1.png" alt-text="显示未关联设备的屏幕截图。":::

1. 使用网格中的筛选器来确定任何设备的“设备模板”列中的值是否为“未关联”。 

1. 选择想要与模板关联的设备：

1. 选择“迁移”：

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-2.png" alt-text="显示如何关联设备的屏幕截图。":::

1. 从可用模板列表中选择模板，然后选择“迁移”。

1. 所选设备与所选设备模板相关联。

## <a name="delete-a-device"></a>删除设备

若要从 Azure IoT Central 应用程序中删除真实设备或模拟设备：

1. 在左侧窗格中选择“设备”。

1. 选择要删除的设备的设备模板。

1. 使用筛选工具来筛选和搜索设备。 选中要删除的设备旁边的框。

1. 选择“删除”  。 可以在“设备操作”面板中跟踪此删除操作的状态。

## <a name="change-a-property"></a>更改属性

云属性是与设备关联的设备元数据，例如城市和序列号。 云属性仅存在于 IoT Central 应用程序中，不会同步到你的设备。 可写属性控制设备的行为，并让你能够远程设置设备的状态，例如，设置恒温器设备的目标温度。  设备属性由设备设置，在 IoT Central 中是只读的。 可以在设备的“设备详细信息”视图中查看和更新属性。

1. 在左侧窗格中选择“设备”。

1. 选择要更改其属性的设备的设备模板，然后选择目标设备。

1. 选择包含设备属性的视图，可在其中输入值并选择页面顶部的“保存”。 此处会显示设备的属性及其当前值。 云属性和可写属性包含可编辑的字段，而设备属性是只读的。 对于可写属性，可在字段底部查看其同步状态。

1. 将属性修改为所需的值。 可以一次性修改多个属性，并可以同时更新所有属性。

1. 选择 **“保存”** 。 如果保存了可写属性，系统会将这些值发送到设备。 当设备确认对可写属性的更改时，状态将恢复为“已同步”。 如果保存了云属性，值将会更新。

## <a name="next-steps"></a>后续步骤

现在，你已了解了如何单独管理设备，建议的下一步是了解如何[在 Azure IoT Central 应用程序中批量管理设备](howto-manage-devices-in-bulk.md)）。
