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
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603229"
---
Azure 文件共享的 SMB 多通道当前具有以下限制：
- 只能与本地冗余 FileStorage 帐户一起使用。
- 仅支持 Windows 客户端。 
- 最大通道数为四。
- 不支持 SMB Direct。
- 不支持使用存储帐户的专用终结点。
- 对于为 Azure 文件存储启用了本地 Active Directory 域服务 (AD DS) 或 Azure AD DS [基于标识的身份验证](../articles/storage/files/storage-files-active-directory-overview.md)的存储帐户，SMB 客户端将无法使用 Windows 文件资源管理器配置对目录和文件的 NTFS 权限。
    - 改为使用 Windows [icacls](/windows-server/administration/windows-commands/icacls) 工具或 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 命令来配置权限。

