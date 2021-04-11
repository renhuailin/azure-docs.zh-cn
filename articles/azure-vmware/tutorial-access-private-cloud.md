---
title: 教程 - 访问私有云
description: 了解如何访问 Azure VMware 解决方案私有云
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494387"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>教程：访问 Azure VMware 解决方案私有云

Azure VMware 解决方案不允许使用本地 vCenter 管理私有云。 你需要通过跳转盒连接到 Azure VMware 解决方案 vCenter 实例。 

在本教程中，我们会在[上一教程](tutorial-configure-networking.md)所创建的资源组中创建一个跳转盒，并登录到 Azure VMware 解决方案 vCenter。 此跳转盒是所创建的同一虚拟网络上的 Windows 虚拟机 (VM)。  它同时提供对 vCenter 和 NSX 管理器的访问。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建用于访问 Azure VMware 解决方案 vCenter 的 Windows 虚拟机
> * 从此虚拟机登录到 vCenter

## <a name="create-a-new-windows-virtual-machine"></a>创建新的 Windows 虚拟机

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>连接到私有云的本地 vCenter

1. 从跳转盒使用云管理员用户名通过 VMware vCenter SSO 登录 vSphere 客户端，并验证用户界面是否成功显示。

1. 在 Azure 门户中，选择你的私有云，然后选择“管理” > “标识”。 

   此时会显示私有云 vCenter 和 NSX-T Manager 的 URL 和用户凭据。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="显示私有云 vCenter 和 NSX 管理器 URL 和凭据。" border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. 导航到在上一步创建的 VM，并连接到该虚拟机。 

   如果不知如何连接到 VM，请参阅[连接到虚拟机](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)以了解详细信息。

1. 在 Windows VM 中，打开浏览器并导航到两个选项卡中的 vCenter 和 NSX-T 管理器 URL。 

1. 在“vCenter”选项卡中，输入在上一步获取的 `cloudadmin@vmcp.local` 用户凭据。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="登录到私有云 vCenter。" border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter 门户。" border="true":::

1. 在浏览器的第二个选项卡中，登录到 NSX-T 管理器。

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="在浏览器的第二个选项卡中，登录到 NSX-T 管理器。" border="true":::



## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建用于连接到 vCenter 的 Windows 虚拟机
> * 从虚拟机登录到 vCenter

继续学习下一教程，了解如何创建虚拟网络，以便为私有云群集设置本地管理。

> [!div class="nextstepaction"]
> [创建虚拟网络](tutorial-configure-networking.md)


