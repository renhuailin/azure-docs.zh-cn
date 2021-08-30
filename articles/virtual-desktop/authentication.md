---
title: Azure 虚拟桌面身份验证 - Azure
description: 适用于 Azure 虚拟桌面的身份验证方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c987ad26d28a6c96d187c420b32bd3103391c62d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228761"
---
# <a name="supported-authentication-methods"></a>支持的身份验证方法

本文简要概述了适用于 Azure 虚拟桌面的身份验证类型。

## <a name="identities"></a>标识

Azure 虚拟桌面支持不同类型的标识，具体取决于你选择的配置。 本部分介绍了可用于每种配置的标识。

### <a name="on-premise-identity"></a>本地标识

由于必须能够通过 Azure Active Directory (Azure AD) 发现用户，用户才能访问 Azure 虚拟桌面，因此不支持仅存在于 Active Directory 域服务 (AD DS) 中的用户标识。 这包括使用 Active Directory 联合身份验证服务 (AD FS) 的独立 Active Directory 部署。

### <a name="hybrid-identity"></a>混合标识

Azure 虚拟桌面通过 Azure AD 支持[混合标识](../active-directory/hybrid/whatis-hybrid-identity.md)，包括使用 AD FS 进行联合身份验证的标识。 可以在 AD DS 中管理这些用户标识，然后使用 [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md) 将其同步到 Azure AD。 还可使用 Azure AD 管理这些标识，并将它们同步到 [Azure AD 目录服务 (Azure AD DS)](../active-directory-domain-services/overview.md)。

使用混合标识访问 Azure 虚拟桌面时，Active Directory (AD) 和 Azure AD 中的用户主体名称 (UPN) 或用户安全标识符 (SID) 有时不匹配。 例如，AD 帐户 user@contoso.local 可能对应于 Azure AD 中的 user@contoso.com。 只有当你的 AD 帐户与 Azure AD 帐户的 UPN 或 SID 匹配时，Azure 虚拟桌面才支持此类型的配置。 SID 是指 AD 中的用户对象属性“ObjectSID”和 Azure AD 中的“OnPremisesSecurityIdentifier”。

### <a name="cloud-only-identity"></a>纯云标识

当使用[已联接 Azure AD 的 VM](deploy-azure-ad-joined-vm.md) 时，Azure 虚拟桌面支持纯云标识。

### <a name="external-identity"></a>外部标识

Azure 虚拟桌面当前不支持[外部标识](../active-directory/external-identities/index.yml)。

## <a name="service-authentication"></a>服务身份验证

若要访问 Azure 虚拟桌面资源，必须先通过登录到 Azure AD 帐户向服务进行身份验证。 订阅工作区以检索你的资源时，或者每次连接到应用或桌面时，都会进行身份验证。 你可以使用[第三方标识提供者](../active-directory/devices/azureadjoin-plan.md#federated-environment)，只要它们通过 Azure AD 进行联合身份验证即可。

### <a name="multifactor-authentication"></a>多重身份验证

按照[在 Azure 虚拟桌面中设置多重身份验证](set-up-mfa.md)中的说明操作，了解如何为部署启用多重身份验证 (MFA)。 该文还将介绍如何配置提示用户输入其凭据的频率。 部署已联接 Azure AD 的 VM 时，请按照[为已联接 Azure AD 的 VM 启用 MFA](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) 中的配置指南进行操作。

### <a name="smart-card-authentication"></a>智能卡身份验证

若要使用智能卡向 Azure AD 进行身份验证，必须首先[为用户证书身份验证配置 AD FS](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)。

## <a name="session-host-authentication"></a>会话主机身份验证

如果尚未启用[单一登录](#single-sign-on-sso)或未在本地保存你的凭据，则还需要向会话主机进行身份验证。 下面是 Azure 虚拟桌面客户端当前针对会话主机支持的登录方法：

- Windows 桌面客户端
    - 用户名和密码
    - 智能卡
    - [Windows Hello 企业版证书信任](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)
    - [使用证书的 Windows Hello 企业版密钥信任](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)
- Windows Store 客户端
    - 用户名和密码
- Web 客户端
    - 用户名和密码
- Android
    - 用户名和密码
- iOS
    - 用户名和密码
- macOS
    - 用户名和密码

Azure 虚拟桌面支持使用 NT LAN Manager (NTLM) 和 Kerberos 进行会话主机身份验证。 智能卡和 Windows Hello 企业版只能使用 Kerberos 进行登录。 为了使用 Kerberos，客户端需从域控制器上运行的密钥分发中心 (KDC) 服务获取 Kerberos 安全票证。 若要获取票证，客户端需要能够直接通过网络看到域控制器。 要实现此目的，你可以通过使用 VPN 连接或设置 [KDC 代理服务器](key-distribution-center-proxy.md)直接在公司网络中进行连接。

### <a name="single-sign-on-sso"></a>单一登录 (SSO)

Azure 虚拟桌面支持[将 Active Directory 联合身份验证服务 (ADFS) 用于 Windows 和 Web 客户端的 SSO](configure-adfs-sso.md)。 使用 SSO，你可以跳过会话主机身份验证。

否则，想要避免系统提示输入会话主机的凭据，唯一方法是将其保存在客户端中。 建议仅在安全设备上执行此操作，以防其他用户访问你的资源。

## <a name="in-session-authentication"></a>会话中身份验证

当你连接到远程应用或桌面后，系统可能会在会话中提示你进行身份验证。 本部分介绍了在此情况下如何使用用户名和密码之外的其他凭据。

### <a name="smart-cards"></a>智能卡

若要在会话中使用智能卡，请确保已在会话主机上安装智能卡驱动程序并启用[智能卡重定向](configure-device-redirections.md#smart-card-redirection)。 请查看[客户端比较图](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare#other-redirection-devices-etc)，以确保你的客户端支持智能卡重定向。

### <a name="fido2-and-windows-hello-for-business"></a>FIDO2 和 Windows Hello 企业版

对于 FIDO2 或 Windows Hello 企业版，Azure 虚拟桌面目前不支持会话中身份验证。

## <a name="next-steps"></a>后续步骤

- 是否想了解其他可确保部署安全性的方法？ 请查看[确保安全的最佳做法](security-guide.md)。
- 连接到已联接到 Azure AD 的 VM 时遇到问题？ 请[排查已联接到 Azure AD 的 VM 的连接问题](troubleshoot-azure-ad-connections.md)。
- 想要从公司网络外部使用智能卡？ 请查看如何设置 [KDC 代理服务器](key-distribution-center-proxy.md)。
