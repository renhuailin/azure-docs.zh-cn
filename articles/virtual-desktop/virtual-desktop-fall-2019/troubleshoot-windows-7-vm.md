---
title: Windows 7 虚拟机 Azure 虚拟桌面（经典）- Azure
description: 如何解决 Azure 虚拟桌面（经典）环境中 Windows 7 虚拟机 (VM) 的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2d0ea39dc4a0b217c35322e0aefc477996402a0f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753856"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-azure-virtual-desktop-classic"></a>排查 Azure 虚拟桌面（经典）中的 Windows 7 虚拟机问题

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。

使用本文来排查在配置 Azure 虚拟桌面会话主机虚拟机 (VM) 时遇到的问题。

## <a name="known-issues"></a>已知问题

Azure 虚拟桌面上的 Windows 7 不支持以下功能：

- 虚拟化应用程序 (RemoteApp)
- 时区重定向
- 自动 DPI 缩放

Azure 虚拟桌面只能虚拟化 Windows 7 的整个桌面。

虽然不支持自动 DPI 缩放，但可以通过在远程桌面客户端中右键单击图标，然后选择“分辨率”来手动更改虚拟机上的分辨率。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>错误：无法访问远程桌面用户组

如果 Azure 虚拟桌面在远程桌面用户组中找不到你或用户的凭据，你可能会看到以下错误消息之一：

- “此用户不是远程桌面用户组的成员”
- “你必须被授予通过远程桌面服务登录的权限”

若要修复此错误，请将用户添加到远程桌面用户组：

1. 打开 Azure 门户。
2. 选择出现错误消息的虚拟机。
3. 选择“运行命令”。
4. 运行以下命令，并将 `<username>` 替换为要添加的用户的名称：

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```