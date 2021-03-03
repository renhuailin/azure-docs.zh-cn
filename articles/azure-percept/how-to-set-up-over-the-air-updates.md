---
title: 设置 Azure IoT 中心以通过无线更新进行部署
description: 了解如何配置 Azure IoT 中心以通过无线方式将更新部署到 Azure Percept 深色
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661857"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>如何将 Azure IoT 中心设置为通过无线更新部署到 Azure Percept 深色
使用无线更新让 Azure Percept 更安全，使其保持最新。 只需几个简单的步骤，便可以使用 IoT 中心的设备更新设置 Azure 环境，并将最新更新部署到 Azure Percept 深色。

## <a name="create-a-device-update-account"></a>创建设备更新帐户

1. 中转到 [Azure 门户](https://portal.azure.com) ，并使用 azure Percept 使用的 azure 帐户登录 

1. 在页面顶部的 "搜索" 窗口中，开始键入 "IoT 中心的设备更新"

1. 在 "搜索" 窗口中显示 **IoT 中心的 "设备更新** "。

1. 单击页面左上角的 " **+ 添加** " 按钮。

1. 选择与 Azure Percept 设备关联的 Azure 订阅和资源组 (这是载入的 IoT 中心位于) 。

1. 指定设备更新帐户的名称和位置

1. 查看详细信息，然后选择 " **查看 + 创建**"。
 
1. 部署完成后，单击 " **前往资源**"。
 
## <a name="create-a-device-update-instance"></a>创建设备更新实例
现在，在设备更新中为 IoT 中心帐户创建一个实例。

1. 在 IoT 中心资源的设备更新中，单击 "**实例管理**" 下的 "**实例**"。
 
1. 单击 " **+ 创建**"，指定实例名称，并选择与 Azure Percept 设备关联的 IoT 中心 (例如，在载入体验) 中创建。 完成此命令可能需要几分钟。
 
1. 单击“创建” 

## <a name="configure-iot-hub"></a>配置 IoT 中心

1. 在 "实例管理 **实例** " 页上，等待设备更新实例移动到 " **成功** " 状态。 单击 "**删除**" 旁边的 "**刷新**" 图标以更新状态。
 
1. 选择已为你创建的实例，然后单击 " **配置 IoT 中心**"。 在左窗格中，选择 " **我同意进行这些更改** ，然后单击" **更新**"。
 
1. 等待进程成功完成。
 
## <a name="configure-access-control-roles"></a>配置访问控制角色
最终步骤使你可以向用户授予发布和部署更新的权限。

1. 在 IoT 中心资源的设备更新中，单击 " **访问控制 (IAM")**
 
2. 单击 " **+ 添加**"，然后选择 "**添加角色分配**"
 
3. 对于 " **角色**"，选择 " **设备更新管理员**"。 用于分配对选择 **用户、组或服务主体** 的 **访问权限**。 对于 **"选择"** 选择你的帐户或将部署更新的人员的帐户。 然后，单击“保存”。 

    > [!TIP]
    > 如果你想要为你的组织中的更多人员提供访问权限，则可以重复此步骤，并使其中的每个用户成为 **设备更新管理员**。

## <a name="next-steps"></a>后续步骤

现在，可以使用 IoT 中心的设备更新来设置和 [更新 Azure Percept 开发工具包](./how-to-update-over-the-air.md) 。 导航到 azure Percept 设备所使用的 Azure IoT 中心。