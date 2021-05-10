---
title: 在 Azure 实验室服务中配置 VM 的自动关闭
description: 本文介绍如何在实验室帐户中配置 VM 的自动关闭。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c0a147a81aaed88313a1b9aa4b0754d9a3badcb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91650028"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>为实验室帐户配置自动关闭 VM 的功能

你可以启用多项“自动关闭”成本控制功能，以前摄性方式防止在虚拟机未被主动使用时产生额外成本。 组合使用以下三个自动关闭和断开连接功能可捕获用户意外使虚拟机运行的大多数情况：
 
- 自动断开用户与 OS 认为空闲的虚拟机的连接。
- 用户断开连接时自动关闭虚拟机。
- 自动关闭已启动但用户未连接的虚拟机。

若要详细了解自动关闭功能，请参阅[通过自动关闭设置最大限度地控制成本](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)部分。

## <a name="enable-automatic-shutdown"></a>启用自动关闭

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到“实验室帐户”页。
1. 在左侧菜单上选择“实验室设置”。
1. 选择适合你的方案的“自动关闭”设置。  

    > [!div class="mx-imgBorder"]
    > ![实验室帐户中的自动关闭设置](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    此设置适用于在实验室帐户中创建的所有实验室。 实验室创建者（教师）可以在实验室级别重写此设置。 在实验室帐户中更改此设置只会影响更改后创建的实验室。

    若要禁用此设置，请取消选中此页上的复选框。 

## <a name="next-steps"></a>后续步骤

若要了解实验室所有者如何在实验室级别配置或替代此设置，请参阅[为实验室配置 VM 自动关闭](how-to-enable-shutdown-disconnect.md)
