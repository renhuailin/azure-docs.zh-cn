---
title: 使用 Azure AD Connect 的“不配置”功能时禁用 PTA | Microsoft Docs
description: 本文介绍了如何使用 Azure AD Connect 的“不配置”功能禁用 PTA。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acabc2cf177ec81ecc293398f9b43f42e71c2862
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234901"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>使用 Azure AD Connect 时禁用 PTA

如果将直通身份验证和 Azure AD Connect 一起使用，并将 Azure AD Connect 设置为“不配置”，则可以禁用直通身份验证。 

>[!NOTE]
>如果已启用 PHS，那么禁用 PTA 将导致租户回退到 PHS。

可以使用以下 cmdlet 来禁用 PTA。 

## <a name="prerequisites"></a>必备条件
需要以下先决条件：
- 安装了 PTA 代理的任何 Windows 计算机。 
- 代理必须为 1.5.1742.0 版本或更高版本。 
- 一个 Azure 全局管理员帐户，用于运行 PowerShell cmdlet 来禁用 PTA。

>[!NOTE]
> 如果代理较旧，则它可能没有完成此操作所需的 cmdlet。 可以从 Azure 门户获取新代理，并将其安装在任意 Windows 计算机上，并提供管理员凭据。 （安装代理不会影响云中的 PTA 状态）

> [!IMPORTANT]
> 如果你使用的是 Azure 政府云，则必须使用以下值传递 ENVIRONMENTNAME 参数。 
>
>| 环境名称 | 云 |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>禁用 PTA
在 PowerShell 会话中，使用以下命令禁用 PTA：
1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 或 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 或 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>如果你无权访问代理

如果你没有代理计算机，可以使用以下命令安装代理。

1. 从 portal.azure.com 下载最新的身份验证代理。
2. 安装该功能：`.\AADConnectAuthAgentSetup.exe` 或 `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 直通身份验证的用户登录](how-to-connect-pta.md)
