---
title: 在 Azure 实验室服务中为实验室配置 VM 的自动关闭功能
description: 了解如何启用或禁用在断开远程桌面连接时自动关闭 VM。
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 8f9080f3b7b762d3b9fa448a903a4167cd2cec4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433946"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>为实验室配置 VM 自动关闭功能

本文介绍如何为实验室配置 VM 的自动关闭功能。

你可以启用多项“自动关闭”成本控制功能，以前摄性方式防止在虚拟机未被主动使用时产生额外成本。 组合使用以下三个自动关闭和断开连接功能可捕获用户意外使虚拟机运行的大多数情况：
 
* 自动断开用户与 OS 认为空闲的虚拟机的连接。
* 用户断开连接时自动关闭虚拟机。
* 自动关闭已启动但用户未连接的虚拟机。

若要详细了解自动关闭功能，请参阅[通过自动关闭设置最大限度地控制成本](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)部分。

实验室帐户管理员可以为你在其中创建实验室的实验室帐户配置此设置。 有关详细信息，请参阅[为实验室帐户配置 VM 自动关闭功能](how-to-configure-lab-accounts.md)。 作为实验室所有者，你可以在创建实验室时或在创建实验室后重写该设置。 

## <a name="configure-for-the-lab-level"></a>针对实验室级别进行配置

可在 [Azure 实验室服务](https://labs.azure.com/)中配置自动关闭设置。

* 创建实验室时（在“实验室策略”中），或者
* 创建实验室后（在“设置”中）

> [!div class="mx-imgBorder"]
> ![在创建实验室时配置](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

请务必在[通过自动关闭设置最大限度地控制成本](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)部分，查看有关自动关闭的详细信息。

> [!WARNING]
> 如果你在断开与 VM 的 RDP 会话连接之前，在 VM 上关闭 Linux 或 Windows 操作系统 (OS)，自动关闭功能将无法正常工作。  
## <a name="next-steps"></a>后续步骤

[实验室的仪表板](use-dashboard.md)
