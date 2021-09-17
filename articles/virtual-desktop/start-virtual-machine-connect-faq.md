---
title: Azure 虚拟桌面启动 VM 连接常见问题解答 - Azure
description: 有关使用“连接时启动 VM”功能的常见问题和最佳做法。
author: Heidilohr
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ba2fcbc404a17b4b31d48b4e7d2e540a875f4f1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444068"
---
# <a name="start-vm-on-connect-faq"></a>Connect 上的 Start VM 常见问题解答

本文介绍了有关 Azure 虚拟桌面主机池连接时启动虚拟机 (VM) 功能的常见问题。

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>当用户停止使用 VM 时，它们是否会自动解除分配？

不是。 你需要配置其他策略，以使用户从他们的会话中注销，并运行 Azure 自动化脚本来解除分配 VM。

配置解除分配策略的方式：

1. 远程连接到要为其设置策略的 VM。

2. 打开“组策略编辑器”，然后前往“本地计算机策略” > “计算机配置” > “管理模板” > “Windows 组件” > “远程桌面服务” > “远程桌面会话主机” > “会话时间限制”。       

3. 找到“为断开连接的会话设置时间限制”策略，然后将其值更改为“已启用”。 

4. 启用策略后，选择“结束已断开连接的会话”。

>[!NOTE]
>请确保将“结束已断开连接的会话”策略的时间限制设置为大于五分钟的值。 如果时间限制值太低，若用户的网络失去连接太长时间，则会中断会话，导致工作丢失。

注销用户不会解除分配其 VM。 若要了解如何解除分配 VM，请参阅[在非工作时间启动或停止 VM](../automation/automation-solution-vm-management.md)（适用于个人主机池）和[使用 Azure 自动化缩放会话主机](set-up-scaling-script.md)（适用于共用主机池）。

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>用户是否可以从他们的客户端中关闭 VM？

是的。 用户可以使用会话中的 "开始" 菜单关闭 VM，就像在物理计算机上那样。 但是，关闭 VM 不会解除分配 VM。 若要了解如何解除分配 VM，请参阅[在非工作时间启动或停止 VM](../automation/automation-solution-vm-management.md)（适用于个人主机池）和[使用 Azure 自动化缩放会话主机](set-up-scaling-script.md)（适用于共用主机池）。

## <a name="next-steps"></a>后续步骤

若要了解如何配置在连接时启动 VM，请参阅[在连接时启动虚拟机](start-virtual-machine-connect.md)。

如果你有关于 Azure 虚拟桌面的更多常规问题，请查看我们的一般[常见问题解答](faq.yml)。
