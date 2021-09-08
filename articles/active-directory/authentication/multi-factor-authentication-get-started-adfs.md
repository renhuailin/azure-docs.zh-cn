---
title: 双重验证 Azure AD MFA 和 ADFS - Azure Active Directory
description: 这是 Azure AD 多重身份验证页，它描述了如何开始使用 Azure AD MFA 和 AD FS。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9abc9e2e2ca2b9305cccf5cec554994c3703ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214491"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Azure AD 多重身份验证和 Active Directory 联合身份验证服务入门

<center>

![Azure AD MFA 和 ADFS 入门](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

如果组织已使用 AD FS 将本地 Active Directory 与 Azure Active Directory 联合，则使用 Azure AD 多重身份验证有两个选项。

* 使用 Azure AD 多重身份验证或 Active Directory 联合身份验证服务保护云资源
* 使用 Azure 多重身份验证服务器保护云和本地资源

下表总结了使用 Azure AD 多重身份验证和 AD FS 保护资源的验证体验

| 验证体验 - 基于浏览器的应用 | 验证体验 - 非基于浏览器的应用 |
|:--- |:--- |
| 使用 Azure AD 多重身份验证保护 Azure AD 资源 |<li>使用 AD FS 在本地执行第一个验证步骤。</li> <li>第二步是使用云身份验证实施基于手机的验证方法。</li> |
| 使用 Active Directory 联合身份验证服务保护 Azure AD 资源 |<li>使用 AD FS 在本地执行第一个验证步骤。</li><li>遵循声明在本地执行第二步。</li> |

有关联合用户的应用密码注意事项：

* 使用云身份验证验证应用密码，因此将绕过联合。 仅当设置应用密码时，才主动使用联合。
* 应用密码不遵循“本地客户端访问控制”设置。
* 使用应用密码时，无法使用本地身份验证日志记录功能。
* 帐户禁用/删除可能需要长达 3 小时才能实现目录同步，从而延迟了云标识中应用密码的禁用/删除。

有关设置 Azure AD 多重身份验证或具有 AD FS 的 Azure 多重身份验证服务器的信息，请参阅以下文章：

* [使用 Azure AD 多重身份验证和 AD FS 保护云资源](howto-mfa-adfs.md)
* [将 Azure 多重身份验证服务器与 Windows Server 配合使用来保护云和本地资源](howto-mfaserver-adfs-windows-server.md)
* [将 Azure 多重身份验证服务器与 AD FS 2.0 配合使用来保护云和本地资源](howto-mfaserver-adfs-2.md)
