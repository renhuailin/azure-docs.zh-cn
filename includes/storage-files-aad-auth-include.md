---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84fa97ec964d490eb9571c7e030704562a4a81d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564097"
---
[Azure 文件存储](../articles/storage/files/storage-files-introduction.md)通过[本地 Active Directory 域服务 (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 和 [Azure Active Directory 域服务 (Azure AD DS)](../articles/active-directory-domain-services/overview.md) 支持服务器消息块 (SMB) 上基于标识的身份验证。 本文重点介绍 Azure 文件共享如何在本地或 Azure 中使用域服务，以支持通过 SMB 对 Azure 文件共享进行基于标识的访问。 通过为 Azure 文件共享启用基于标识的访问，你可以将现有文件服务器替换为 Azure 文件共享，而无需替换现有的目录服务，从而保持用户对共享的无缝访问。 

Azure 文件存储强制授予用户对共享和目录/文件级别的访问权限。 可以对通过 [Azure 基于角色的访问控制 (Azure RBAC)](../articles/role-based-access-control/overview.md) 模型管理的 Azure Active Directory (Azure AD) 用户或组执行共享级别权限分配。 对于 RBAC，用于访问文件的凭据应该可用于或同步到 Azure AD。 可以将“存储文件数据 SMB 共享读取者”等 Azure 内置角色分配给 Azure AD 中的用户或组，以授予对 Azure 文件共享的读取访问权限。

在目录/文件级别，Azure 文件存储支持预留、继承和强制执行 [Windows DACL](/windows/win32/secauthz/access-control-lists)，就像任何 windows 文件服务器一样。 在现有文件共享和 Azure 文件共享之间通过 SMB 复制数据时，可以选择保留 Windows DACL。 无论你是否打算强制执行授权，都可以使用 Azure 文件共享来备份 ACL 和数据。