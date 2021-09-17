---
title: 教程 - 访问私有云
description: 了解如何访问 Azure VMware 解决方案私有云
ms.topic: tutorial
ms.date: 08/13/2021
ms.openlocfilehash: 6b4798bf5257be82475986c040b04b63ff5a483b
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070786"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>教程：访问 Azure VMware 解决方案私有云

Azure VMware 解决方案不允许使用本地 vCenter 管理私有云。 相反，你需要通过跳转盒连接到 Azure VMware 解决方案 vCenter 实例。 

在本教程中，我们会在[上一教程](tutorial-configure-networking.md)所创建的资源组中创建一个跳转盒，并登录到 Azure VMware 解决方案 vCenter。 此跳转盒是所创建的同一虚拟网络上的 Windows 虚拟机 (VM)。  它同时提供对 vCenter 和 NSX 管理器的访问。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建用于访问 Azure VMware 解决方案 vCenter 的 Windows VM
> * 从此 VM 登录到 vCenter

## <a name="create-a-new-windows-virtual-machine"></a>创建新的 Windows 虚拟机

1. 在资源组中，选择“添加”，搜索“Microsoft Windows 10”，然后选择它 。 然后选择“创建”。

   :::image type="content" source="media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="说明如何为跳转盒添加新的 Windows 10 VM 的屏幕截图。":::

1. 在字段中输入所需信息，然后选择“查看 + 创建”。 

   有关字段的详细信息，请参阅下表。

   | 字段 | 值 |
   | --- | --- |
   | **订阅** | 为“值”预填充了属于资源组的订阅。 |
   | **资源组** | 为当前资源组（在上一教程中创建）预填充了值。  |
   | **虚拟机名称** | 输入 VM 的唯一名称。 |
   | **区域** | 选择 VM 的地理位置。 |
   | **可用性选项** | 保留选择的默认值。 |
   | **图像** | 选择 VM 映像。 |
   | **大小** | 保留默认大小值。 |
   | **身份验证类型**  | 选择“密码”。 |
   | **用户名** | 输入用于登录到 VM 的用户名。 |
   | **密码** | 输入用于登录到 VM 的密码。 |
   | **确认密码** | 输入用于登录到 VM 的密码。 |
   | **公共入站端口** | 选择“无”。 <ul><li>若要仅在需要访问 VM 时才控制对 VM 的访问，请使用 [JIT 访问](../security-center/security-center-just-in-time.md#jit-configure)。</li><li>若要从 Internet 安全地访问跳转盒服务器而不公开任何网络端口，请使用 [Azure Bastion](../bastion/tutorial-create-host-portal.md)。</li></ul>  |


1. 验证通过后，选择“创建”启动虚拟机创建过程。

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>连接到私有云的本地 vCenter

1. 从跳转盒使用云管理员用户名通过 VMware vCenter SSO 登录 vSphere 客户端，并验证用户界面是否成功显示。

1. 在 Azure 门户中，选择你的私有云，然后选择“管理” > “标识”。 

   此时会显示私有云 vCenter 和 NSX-T Manager 的 URL 和用户凭据。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="私有云 vCenter 以及 NSX 管理器 URL 和凭据的屏幕截图。" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. 导航到在上一步创建的 VM，并连接到该虚拟机。 

   如果不知如何连接到 VM，请参阅[连接到虚拟机](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)以了解详细信息。

1. 在 Windows VM 中，打开浏览器并导航到两个选项卡中的 vCenter 和 NSX-T Manager URL。 

1. 在“vCenter”选项卡中，输入在上一步获取的 `cloudadmin@vsphere.local` 用户凭据。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="显示 VMware vSphere 登录页的屏幕截图。" border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="显示 vSphere 客户端中的群集 1 摘要的屏幕截图。" border="true":::

1. 在浏览器的第二个选项卡中，登录到 NSX-T Manager。

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="NSX-T 管理器概述的屏幕截图。" border="true":::



## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建用于连接到 vCenter 的 Windows VM
> * 从此 VM 登录到 vCenter

继续学习下一教程，了解如何创建虚拟网络，以便为私有云群集设置本地管理。

> [!div class="nextstepaction"]
> [创建虚拟网络](tutorial-configure-networking.md)

