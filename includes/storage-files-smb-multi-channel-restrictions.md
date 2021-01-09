---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052813"
---
Azure 文件共享的 SMB 多通道当前具有以下限制：
- 只能与本地冗余 FileStorage 帐户一起使用。
- 仅适用于 Windows 客户端。 
- 最大通道数为四。
- 不支持 SMB Direct。
- 不支持存储帐户的专用终结点。
- 对于本地 Active Directory 域服务的存储帐户 (为 Azure 文件启用 AD DS) 或 Azure AD DS [基于身份的身份验证](../articles/storage/files/storage-files-active-directory-overview.md) ，SMB 客户端将无法使用 Windows 文件资源管理器配置对目录和文件的 NTFS 权限。
    - 改为使用 Windows [icacls](/windows-server/administration/windows-commands/icacls) 工具或 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) 命令来配置权限。

