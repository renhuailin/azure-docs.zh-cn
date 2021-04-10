---
title: 创建 Azure VMware 解决方案跳转盒
description: 创建 Azure VMware 解决方案跳转盒的步骤。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462233"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. 在资源组中选择“+ 添加”，搜索并选择“Microsoft Windows 10”，然后选择“创建”  。

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="为跳转盒添加新的 Windows 10 VM。" border="true":::

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
   | **公共入站端口** | 选择“无”。 如果选择“无”，可以使用 [JIT 访问](../../security-center/security-center-just-in-time.md#jit-configure)，以便仅在需要访问 VM 时才控制对 VM 的访问。 或者，如果你想从 Internet 安全地访问跳转盒服务器而不公开任何网络端口，则可以使用 [Azure Bastion](../../bastion/tutorial-create-host-portal.md)。  |


1. 验证通过后，选择“创建”启动虚拟机创建过程。

