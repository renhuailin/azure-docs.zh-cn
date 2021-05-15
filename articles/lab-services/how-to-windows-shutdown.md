---
title: Azure 实验室服务中的 Windows 关闭行为控制指南 | Microsoft Docs
description: 自动关闭空闲 Windows 虚拟机并删除 Windows 关闭命令的步骤。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647692"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows 关闭行为控制指南

Azure 实验室服务提供多个成本控件，以确保 Windows 虚拟机 (VM) 不会意外运行：
 - [设置日程安排](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [为用户设置配额](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [启用在断开连接时自动关闭](./how-to-enable-shutdown-disconnect.md)

即使使用这些成本控件，在某些情况下，Windows VM 还是可能会意外地继续运行；因此，会从学生配额中扣减：

- **RDP 窗口处于开启状态**
  
    当学生使用 RDP 连接到其 VM 时，可能会无意中使 RDP 窗口开启。  只要 RDP 窗口保持开启，“断开连接时自动关闭”设置就永远不会起作用，因为它仅在 RDP 会话断开连接后触发。

- **Windows 关闭命令用于关闭 VM**
  
    学生可以使用 Windows 关闭命令或 Windows 中提供的其他关闭机制来关闭 VM，而不是使用 [Azure 实验室服务的“停止”按钮](./how-to-use-classroom-lab.md#start-or-stop-the-vm)。  发生这种情况时，从 Azure 实验室服务的角度来看，VM 仍在使用。
    
为了帮助防止出现这些情况，本指南提供了自动关闭空闲 Windows VM 并从“开始”菜单中删除 Windows 关闭命令的步骤。  

> [!NOTE]
> 当学生启动 VM 时，VM 也可能会意外地从配额中扣减，但绝不会真正使用 RDP 连接到该 VM。  本指南目前不会处理这种情况。  相反，应提醒学生在启动后立即使用 RDP 连接到其 VM；或者，应停止 VM。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>从“开始”菜单中删除 Windows 关闭命令

使用 Windows“本地组策略”设置，还可以从“开始”菜单中删除关闭命令。

若要删除关闭命令，可以连接到模板 VM 并执行以下 PowerShell 脚本。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者，可以选择使用模板 VM 执行以下手动步骤：

1. 按 Windows 键，键入 gpedit.msc，然后选择“编辑组策略（控制面板）”。

1. 转到“计算机配置”>“管理模板”>“开始菜单和任务栏”。  

    ![本地组策略编辑器](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 右键单击“删除并阻止访问 Shut Down、Restart、Sleep 和 Hibernate 命令”，然后单击“编辑”。

1. 选择“已启用”设置，然后单击“确定”：
 
   ![关闭设置](./media/how-to-windows-shutdown/edit-shutdown.png)

1. 请注意，关闭命令不再显示在 Windows“开始”菜单下；仅显示“断开连接”命令。

    ![关闭命令](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>后续步骤
请参阅有关如何准备 Windows 模板 VM 的文章：《[Azure 实验室服务中 Windows 模板计算机设置指南](how-to-prepare-windows-template.md)》