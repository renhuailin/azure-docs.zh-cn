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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995454"
---
Azure 文件共享的 SMB 多通道当前具有以下限制：
- 只能与本地冗余 FileStorage 帐户一起使用。
- 仅适用于 Windows 客户端。 
- 最大通道数为四。
- 不支持 SMB Direct。
- 对于本地 Active Directory 域服务的存储帐户 (为 Azure 文件启用 AD DS) 或 Azure AD DS [基于身份的身份验证](../articles/storage/files/storage-files-active-directory-overview.md) ，SMB 客户端将无法使用 Windows 文件资源管理器配置对目录和文件的 NTFS 权限。
    - 改为使用 Windows [icacls](/windows-server/administration/windows-commands/icacls) 工具或 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) 命令来配置权限。

