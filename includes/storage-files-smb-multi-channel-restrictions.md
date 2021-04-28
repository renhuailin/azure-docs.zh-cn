---
title: 包含文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/26/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 04ded4340eef0bfe5bc91ee2e3f2552975df2e05
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065594"
---
Azure 文件共享的 SMB 多通道当前具有以下限制：
- 仅支持 Windows 客户端。 
- 最大通道数为四。
- 不支持 SMB Direct。
- 不支持使用存储帐户的专用终结点。
- 对于为 Azure 文件存储启用了本地 Active Directory 域服务 (AD DS) 或 Azure AD DS [基于标识的身份验证](../articles/storage/files/storage-files-active-directory-overview.md)的存储帐户，SMB 客户端将无法使用 Windows 文件资源管理器配置对目录和文件的 NTFS 权限。
    - 改为使用 Windows [icacls](/windows-server/administration/windows-commands/icacls) 工具或 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 命令来配置权限。

