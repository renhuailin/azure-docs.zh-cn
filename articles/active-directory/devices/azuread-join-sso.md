---
title: 本地资源的 SSO 在已加入 Azure AD 的设备上的工作原理 | Microsoft Docs
description: 了解如何通过配置已加入混合 Azure Active Directory 的设备来扩展 SSO 体验。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a48cf9d6912fae91cd789ae16bb68e1d95a4a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585420"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>本地资源的 SSO 在已加入 Azure AD 的设备上的工作原理

已加入 Azure Active Directory (Azure AD) 的设备为租户的云应用提供单一登录 (SSO) 体验可能并不意外。 如果环境具有本地 Active Directory (AD)，你还可以在已加入 Azure AD 的设备上获得对依赖于本地 AD 的资源和应用程序的 SSO 体验。 

本文介绍它的工作原理。

## <a name="prerequisites"></a>先决条件

本地 SSO 需要与本地 AD DS 域控制器进行视距通信。 如果已加入 Azure AD 的设备未连接到组织的网络，则需要 VPN 或其他网络基础结构。 

## <a name="how-it-works"></a>工作原理 

使用已加入 Azure AD 的设备，用户已在环境享有云应用的 SSO 体验。 如果环境具有一个 Azure AD 和一个本地 AD，建议将 SSO 体验的范围扩展到本地业务线 (LOB) 应用、文件共享和打印机。

已加入 Azure AD 的设备不了解你的本地 AD 环境，因为它们未加入其中。 但是，可以使用 Azure AD Connect 向这些设备提供本地 AD 的其他信息。

如果拥有同时具有 Azure AD 和本地 AD 的混合环境，那么你很可能已部署了 Azure AD Connect 以将本地标识信息同步到云。 作为同步过程的一部分，Azure AD Connect 将本地用户和域信息同步到 Azure AD。 当用户登录到混合环境中的已加入 Azure AD 的设备时：

1. Azure AD 会将用户的本地域的详细信息以及[主刷新令牌](concept-primary-refresh-token.md)发送回设备
1. 本地安全机构 (LSA) 服务在该设备允许进行 Kerberos 和 NTLM 身份验证。

>[!NOTE]
> Windows Hello for Business 需要其他配置才能支持已加入 Azure AD 的设备的本地 SSO。 有关详细信息，请参阅[使用 Windows Hello for Business 配置已加入 Azure AD 的设备进行本地单一登录](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)。 
>
> 使用 Windows 10 的基于 FIDO2 安全密钥的无密码身份验证需要额外的配置，才能从已加入 Azure AD 的设备启用本地 SSO。 有关详细信息，请参阅[使用 Azure Active Directory 启用无密码安全密钥登录到本地资源](../authentication/howto-authentication-passwordless-security-key-on-premises.md)。 

在尝试访问用户本地环境中请求 Kerberos 或 NTLM 的资源期间，设备：

1. 向找到的 DC 发送本地域信息和用户凭据，以对用户进行身份验证。
1. 基于本地资源或应用程序支持的协议，接收 Kerberos [票证授予票证 (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) 或 NTLM 令牌。 如果尝试获取域的 Kerberos TGT 或 NTLM 令牌失败（相关 DCLocator 超时会导致延迟），则会尝试使用凭据管理器条目，或者用户可能会收到请求目标资源凭据的身份验证弹出窗口。

当用户尝试访问针对 Windows 集成身份验证配置的所有应用时，它们将顺利进行 SSO。

## <a name="what-you-get"></a>提供的服务

通过 SSO，在已加入 Azure AD 的设备上，可以： 

- 访问 AD 成员服务器上的 UNC 路径
- 访问为 Windows 集成安全性配置的 AD 成员 Web 服务器 

如果想要管理 Windows 设备的本地 AD，请安装[适用于 Windows 10 的远程服务器管理工具](https://www.microsoft.com/download/details.aspx?id=45520)。

可用工具如下：

- Active Directory 用户和计算机 (ADUC) 管理单元管理所有 AD 对象。 但是，需要手动指定要连接到的域。
- DHCP 管理单元用于管理已加入 AD 的 DHCP 服务器。 但是，可能需要指定 DHCP 服务器名称或地址。
 
## <a name="what-you-should-know"></a>要点

可能需要在 Azure AD Connect 中调整[基于域的筛选](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)，以确保所需域的相关数据已同步。

依赖 Active Directory 计算机身份验证的应用和资源无法正常运行，因为已加入 Azure AD 的设备在 AD 中没有计算机对象。 

不能与已加入 Azure AD 的设备上的其他用户共享文件。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure Active Directory 中的设备管理是什么？](overview.md)