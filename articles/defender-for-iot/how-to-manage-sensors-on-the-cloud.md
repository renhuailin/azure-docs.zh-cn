---
title: 在用于 IoT 的 Defender 门户中载入和管理传感器和订阅
description: 了解如何在用于 IoT 的 Defender 门户中加入、查看和管理传感器。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 63b3b450e289b40aa9acbfb0d5170e8eb57f9e58
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733255"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>在用于 IoT 的 Defender 门户中载入和管理传感器和订阅

本文介绍如何在 [用于 IoT 的 Defender 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中载入、查看和管理传感器。

## <a name="onboard-sensors"></a>加入传感器

通过将传感器注册到 Azure Defender 用于 IoT 并下载传感器激活文件，来载入传感器。

### <a name="register-the-sensor"></a>注册传感器

若要注册，请执行以下操作：

1. 中转到 [IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中的 "**欢迎**" 页。
1. 选择 " **板载传感器**"。
1. 创建传感器名称。 建议你将安装的传感器的 IP 地址包括为名称的一部分，或者使用易于识别的名称。 这将确保在 [IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 的 Azure Defender 中注册名称与传感器控制台中显示的已部署传感器的 IP 之间更轻松地进行跟踪和一致的命名。
1. 将传感器与 Azure 订阅相关联。
1. 使用 **云连接** 的切换器选择传感器管理模式。 如果开启切换，则传感器连接到云。 如果切换功能处于关闭状态，则传感器为本地管理。

   - **云连接传感器**：传感器检测到的信息显示在传感器控制台中。 警报信息通过 IoT 中心传送，可与其他 Azure 服务（如 Azure Sentinel）共享。

   - **本地托管传感器**：传感器检测的信息将显示在传感器控制台中。 如果使用的是气流网络，并且想要统一查看多个本地托管传感器检测到的所有信息，请使用本地管理控制台。

   对于云连接传感器，在载入过程中定义的名称是在传感器控制台中显示的名称。 不能直接从控制台更改此名称。 对于本地托管的传感器，在载入过程中应用的名称将存储在 Azure 中，并可在传感器控制台中更新。

1. 选择一个 IoT 中心作为该传感器与用于 IoT 的 Azure Defender 之间的网关。
1. 如果传感器已连接到云，请将其与 IoT 中心相关联，然后定义站点名称和区域。 您还可以添加描述性标记。 站点名称、区域和标记是 " [站点和传感器" 页](#view-onboarded-sensors)上的描述性条目。

### <a name="download-the-sensor-activation-file"></a>下载传感器激活文件

传感器激活文件包含有关传感器的管理模式的说明。 为每个部署的传感器下载唯一的激活文件。 首次登录传感器控制台的用户将激活文件上传到传感器。

下载激活文件：

1. 在 " **板载传感器** " 页上，选择 " **下载激活文件**"。
1. 使文件可供用户首次登录到传感器控制台。

## <a name="view-onboarded-sensors"></a>查看载入传感器

在 [IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)上，可以查看有关载入传感器的基本信息。

1. 选择 **站点和传感器**。
1. 使用筛选器和搜索工具查找所需的传感器和威胁智能信息。

- 载入了多少传感器
- 云连接和本地管理的传感器的数目
- 与载入传感器关联的集线器
- 添加了有关传感器的详细信息，例如在载入期间分配给传感器的名称、与传感器相关联的区域或使用标记添加的其他描述性信息

## <a name="manage-onboarded-sensors"></a>管理载入传感器

使用适用于 [IoT 门户的 Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 管理任务与传感器相关的管理任务。

可以在 " **站点和传感器** " 页上查看载入传感器。 你还可以在此页中编辑传感器信息。

### <a name="export-sensor-details"></a>导出传感器详细信息

若要导出载入传感器信息，请选择 "**站点和传感器**" 页顶部的 "**导出**" 图标。

### <a name="edit-sensor-zone-details"></a>编辑传感器区域详细信息

使用 " **站点和传感器** " 编辑选项可以编辑传感器名称和区域。

编辑：

1. 选择要编辑的传感器的 **省略号** **(") "。**
1. 选择“编辑”  。
1. 更新传感器区域，或创建新的区域。

### <a name="delete-a-sensor"></a>删除传感器

如果删除云连接的传感器，则不会将信息发送到 IoT 中心。 如果不再使用本地连接的传感器，请将其删除。

删除传感器：

1. 选择要删除的传感器的 **省略号 (") "。**
1. 确认删除。

### <a name="reactivate-a-sensor"></a>重新激活传感器 

可能需要重新激活传感器，因为需要：

- **在云连接模式下工作，而不是在本地托管模式**：重新激活后，传感器检测在传感器中显示，新检测到的警报信息通过 IoT 中心传送。 此信息可与其他 Azure 服务（如 Azure Sentinel）共享。

- **在本地托管模式下工作，而不是云连接模式**：重新激活后，传感器检测信息只显示在传感器中。

- **将传感器关联到新的 IoT 中心**：若要执行此操作，请使用新的集线器重新注册传感器，然后下载新的激活文件。

重新激活传感器：

1. 在 [IoT 门户的 Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)上，请参阅 **站点和传感器** 页面。

2. 选择要为其上传新的激活文件的传感器。

3. 删除传感器。

4. 通过从 "入门" 页中选择 "载入 **传感器** "，在新模式下或通过新的 IoT 中心再次载入传感器。

5. 下载激活文件。

1. 登录到 "用于 IoT 的 Defender 传感器" 控制台。

7. 在传感器控制台中，选择“系统设置”，然后选择“重新激活”。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="上传激活文件以重新激活传感器。":::

8. 选择 " **上传** "，然后从 "板载传感器" 页中选择保存的文件。

9. 选择“激活”  。

## <a name="offboard-a-subscription"></a>下架订阅

每月管理一次订阅。 在下架订阅时，将对该订阅计费，直到月底结束。 

在脱离订阅之前，卸载与该订阅关联的所有传感器。 有关如何删除传感器的详细信息，请参阅 [删除传感器](#delete-a-sensor)。 

下架订阅：

1. 导航到 " **定价** " 页。
1. 选择订阅，然后选择 " **删除** " 图标 :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: 。
1. 在确认弹出窗口中，选中相应的复选框以确认已删除与该订阅关联的所有传感器。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="选中该复选框，然后选择 &quot;下架&quot; 下架传感器。":::

1. 选择 " **下架** " 按钮。 

本地环境不受影响，但你应该从本地环境中卸载传感器，或将传感器重新分配给另一个订阅，以便阻止任何相关数据流动到本地管理控制台。 

## <a name="see-also"></a>另请参阅

[激活和设置传感器](how-to-activate-and-set-up-your-sensor.md)
