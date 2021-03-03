---
title: 在 Azure IoT 中心的设备更新中创建设备更新帐户 |Microsoft Docs
description: 在 Azure IoT 中心的设备更新中创建设备更新帐户。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0201882b74d2acf57c246d2dc63530ca8b1f010
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692507"
---
# <a name="device-update-for-iot-hub-resource-management"></a>IoT 中心资源管理的设备更新

若要开始使用设备更新，需要创建设备更新帐户、实例和设置访问控制角色。 

## <a name="prerequisites"></a>先决条件

* 对 IoT 中心的访问权限。 建议使用 S1 (标准) 层或更高版本。 
* 支持的浏览器：
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>创建设备更新帐户

1. 转到 [Azure 门户](https://portal.azure.com)

2. 单击 "创建资源"，然后搜索 "IoT 中心的设备更新"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="IoT 中心资源的设备更新的屏幕截图。" lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. 单击 "为 IoT 中心创建-> 设备更新"

4. 指定要与设备更新帐户和资源组相关联的 Azure 订阅

5. 指定设备更新帐户的名称和位置

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="帐户详细信息的屏幕截图。" lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > 可以 [按区域访问 Azure 产品页](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) ，以发现 IoT 中心的设备更新可用的区域。 如果你所在地区的 IoT 中心的设备更新不可用，则可以选择在离你最近的可用区域中创建帐户。 

6. 单击 "下一步：查看 + 创建>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="帐户详细信息评审的屏幕截图。" lightbox="media/create-device-update-account/account-review.png":::

7. 查看详细信息，然后选择 "创建"。 你将看到你的部署正在进行中。 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="正在进行帐户部署的屏幕截图。" lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. 几分钟后，你将看到部署状态更改为 "完成"。 单击 "前往资源"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="帐户部署的屏幕截图完成。" lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>创建设备更新实例 

设备更新的实例与单个 IoT 中心关联。 选择将与设备更新一起使用的 IoT 中心。 在此步骤中，我们将创建一个新的共享访问策略，以确保设备更新只使用所需的权限来处理 IoT 中心 (注册表写入和服务连接) 。 此策略可确保访问仅限于设备更新。

创建帐户后创建设备更新实例。

1. 进入新创建的帐户资源后，请切换到实例管理 "实例" 边栏选项卡

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="帐户内实例管理的屏幕截图。" lightbox="media/create-device-update-account/instance-blade.png":::

2. 单击 "创建并指定实例名称" 并选择 IoT 中心

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="实例详细信息的屏幕截图。" lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > 你链接到设备更新资源的 IoT 中心不需要与设备更新帐户位于同一区域。 但是，为了获得更好的性能，建议 IoT 中心位于与设备更新帐户所在的区域相同或接近的区域。 

3. 单击“创建”。 你将看到实例处于 "正在创建" 状态。 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="创建实例的屏幕截图。" lightbox="media/create-device-update-account/instance-creating.png":::

4. 等待实例部署完成时，5-10 分钟。 刷新状态，直到看到 "预配状态" 变为 "成功"。

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="已成功创建实例的屏幕截图。" lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>配置 IoT 中心 

为了让设备更新从 IoT 中心接收更改通知，设备更新与 "内置" 事件中心集成。 单击 "配置 IoT 中心" 按钮将配置所需的消息路由和与 IoT 设备通信所需的访问策略。 

配置 IoT 中心

1. 实例 "预配状态" 变为 "成功" 后，请在 "实例管理" 边栏选项卡中选择该实例。 单击 "配置 IoT 中心"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="为实例配置 IoT 中心的屏幕截图。" lightbox="media/create-device-update-account/instance-configure.png":::

2. 选择 "我同意进行这些更改"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="已同意为实例配置 IoT 中心的屏幕截图。" lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. 单击 "更新"

[了解配置的消息路由。](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>配置访问控制角色

为了使其他用户有权访问设备，必须为用户授予对此资源的访问权限。 

1. 在设备更新帐户中访问 (IAM) 的访问控制

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="设备更新帐户中的访问控制屏幕截图。" lightbox="media/create-device-update-account/account-access-control.png":::

2. 单击 "添加角色分配"

3. 在 "选择角色" 下，从给定选项中选择设备更新角色
     - 设备更新管理员
     - 设备更新读取器
     - 设备更新内容管理员
     - 设备更新内容读取器
     - 设备更新部署管理员
     - 设备更新部署读取器
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="设备更新帐户中的访问控制角色分配的屏幕截图。" lightbox="media/create-device-update-account/role-assignment.png":::
    
    [了解 IoT 中心设备更新中基于角色的访问控制](device-update-control-access.md) 
    
4. 向用户或 Azure AD 组分配访问权限
5. 点击“保存”(Save)
6. 你现在已准备好在 IoT 中心内使用设备更新体验

## <a name="next-steps"></a>后续步骤

[使用 IoT 中心的设备更新导入更新。](import-update.md)

[了解设备更新帐户和实例。](device-update-resources.md) 

[了解设备更新访问控制角色。 ](device-update-control-access.md) 

