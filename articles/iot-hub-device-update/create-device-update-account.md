---
title: 在适用于 Azure IoT 中心的设备更新中创建设备更新帐户 |Microsoft Docs
description: 在适用于 Azure IoT 中心的设备更新中创建设备更新帐户。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.custom: subject-rbac-steps
ms.openlocfilehash: 5ad119017f4fe1dda0703547480c7444978d1878
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129016"
---
# <a name="device-update-for-iot-hub-resource-management"></a>IoT 中心设备更新资源管理

若要开始使用设备更新，需要创建设备更新帐户、实例并设置访问控制角色。 

## <a name="prerequisites"></a>先决条件

* 对 IoT 中心的访问权限。 建议使用 S1（标准）层或更高层级。 
* 支持的浏览器：
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>创建设备更新帐户

1. 转到 [Azure 门户](https://portal.azure.com)

2. 单击“创建资源”，然后搜索“Device Update for IoT Hub”

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="适用于 IoT 中心资源的设备更新的屏幕截图。" lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. 单击“创建” -> “Device Update for IoT Hub” 

4. 指定要与设备更新帐户和资源组关联的 Azure 订阅

5. 指定设备更新帐户的名称和位置

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="帐户详细信息的屏幕截图。" lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > 可以转到[按区域划分的 Azure 产品页](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)，以发现提供适用于 IoT 中心的设备更新的区域。 如果你所在地区不提供适用于 IoT 中心的设备更新，则可以选择在离你最近的可用区域中创建帐户。 

6. （可选）可以选中此框，将“设备更新管理员角色”分配给自己。 还可使用“配置访问控制角色”部分中列出的步骤，为用户和应用程序提供适当访问级别的角色组合。

8. 单击“下一步: 查看 + 创建 >”

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="帐户详细信息检查的屏幕截图。" lightbox="media/create-device-update-account/account-review.png":::

7. 检查详细信息，然后选择“创建”。 你会看到正在进行部署。 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="正在进行帐户部署的屏幕截图。" lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. 几分钟后，你将看到部署状态更改为“完成”。 单击“转到资源”

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="帐户部署已完成的屏幕截图。" lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>创建设备更新实例 

设备更新的实例与单个 IoT 中心关联。 选择将与设备更新配合使用的 IoT 中心。 我们将在这一步的过程中创建新的共享访问策略，以确保设备更新只使用所需权限来使用 IoT 中心（注册表写入和服务连接）。 此策略确保访问仅限于设备更新。

若要在创建帐户后创建设备更新实例，请执行以下操作。

1. 在进入新创建的帐户资源后，请转到实例管理“实例”边栏选项卡

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="帐户内实例管理的屏幕截图。" lightbox="media/create-device-update-account/instance-blade.png":::

2. 单击“创建”，然后指定实例名称并选择 IoT 中心

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="实例详细信息的屏幕截图。" lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > 链接到设备更新资源的 IoT 中心不需要与设备更新帐户位于同一区域。 但是，为了获得更好的性能，建议 IoT 中心与设备更新帐户位于相同区域，或者两个区域接近。 

3. 单击“创建”。 你将会看到实例处于“正在创建”状态。 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="创建实例的屏幕截图。" lightbox="media/create-device-update-account/instance-creating.png":::

4. 请等待 5-10 分钟，让实例部署完成。 刷新状态，直到看到“预配状态”变为“已成功”。

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="已成功创建实例的屏幕截图。" lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>配置 IoT 中心 

为了让设备更新从 IoT 中心收到更改通知，设备更新与“内置”的事件中心集成。 单击“配置 IoT 中心”按钮可配置所需消息路由以及与 IoT 设备通信所需的访问策略。 

配置 IoT 中心

1. 在实例“预配状态”变为“已成功”后，请在“实例管理”边栏选项卡中选择该实例。 单击“配置 IoT 中心”

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="为实例配置 IoT 中心的屏幕截图。" lightbox="media/create-device-update-account/instance-configure.png":::

2. 选择“我同意进行这些更改”

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="同意为实例配置 IoT 中心的屏幕截图。" lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. 单击“更新”  。

   > [!NOTE] 
   > 如果你使用的是免费的 Azure IoT 中心层，则允许的消息路由数限制为 5。 Device Update for IoT Hub 需要配置 4 条消息路由才能正常工作。 

[了解接受配置的消息路由。](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>配置访问控制角色

为了使其他用户有权访问设备更新，必须为用户授予对此资源的访问权限。 如果在帐户创建过程中为自己分配了“设备更新管理员角色”，并且不需要向其他用户或应用程序提供访问权限，可以跳过此步骤。 

1. 在设备更新帐户中转到访问控制 (IAM)

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="设备更新帐户中的访问控制的屏幕截图。" lightbox="media/create-device-update-account/account-access-control.png":::

2. 单击“添加角色分配”

3. 在“角色”选项卡下，从提供的选项中选择设备更新角色
     - 设备更新管理员
     - 设备更新读取者
     - 设备更新内容管理员
     - 设备更新内容读取者
     - 设备更新部署管理员
     - 设备更新部署读取者
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="设备更新帐户中的访问控制角色分配的屏幕截图。" lightbox="media/create-device-update-account/role-assignment.png":::
    
    [了解适用于 IoT 中心的设备更新中基于角色的访问控制](device-update-control-access.md) 
    
4. 点击“下一步” 
5. 为用户或 Azure AD 组分配访问权限
6. 选择成员
   
   :::image type="content" source="media/create-device-update-account/role-assignment-2.png" alt-text="设备更新帐户中访问控制成员选择的屏幕截图。" lightbox="media/create-device-update-account/role-assignment-2.png":::

6. 单击“查看 + 分配”
7. 查看新角色分配，并再次单击“查看 + 分配”
8. 你现在已准备好从 IoT 中心内使用设备更新体验

## <a name="next-steps"></a>后续步骤

请尝试使用下列快速教程之一更新设备：

 - [模拟器上的设备更新](device-update-simulator.md)
 - [Raspberry Pi 上的设备更新](device-update-raspberry-pi.md)
 - [Ubuntu 服务器 18.04 x64 包代理上的设备更新](device-update-ubuntu-agent.md)

[了解设备更新帐户和实例。](device-update-resources.md) 

[了解设备更新访问控制角色。](device-update-control-access.md) 

