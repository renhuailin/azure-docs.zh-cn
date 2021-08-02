---
title: Azure 虚拟桌面身份验证 - Azure
description: 适用于 Azure 虚拟桌面的身份验证方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: bb5341f1f80350ce14b99a6ca5cf27343bd20675
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745378"
---
# <a name="supported-authentication-methods"></a>支持的身份验证方法

本文简要概述了适用于 Azure 虚拟桌面的身份验证类型。

## <a name="session-host-authentication"></a>会话主机身份验证

Azure 虚拟桌面支持使用 NT LAN Manager (NTLM) 和 Kerberos 进行会话主机身份验证。 但是，在使用 Kerberos 时，客户端需从域控制器上运行的密钥分发中心 (KDC) 服务获取 Kerberos 安全票证。 若要获取票证，客户端需要直线连接到域控制器。 通过使用企业网络，即可实现直连。 还可以通过 VPN 连接至企业网络，或者设置 [KDC 代理服务器](key-distribution-center-proxy.md)。

以下是当前受支持的登录方法：

- Windows 桌面客户端
    - 用户名和密码
    - 智能卡
    - Windows Hello 企业版（仅限证书信任）
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

>[!NOTE]
>智能卡和 Windows Hello 企业版只能使用 Kerberos 进行登录。 使用 Kerberos 登录时，需要直接连接至域控制器或 [KDC 代理服务器](key-distribution-center-proxy.md)。

## <a name="hybrid-identity"></a>混合标识

Azure 虚拟桌面支持使用 Azure Active Directory (AD) 的[混合标识](../active-directory/hybrid/whatis-hybrid-identity.md)，包括使用 Active Directory 联合身份验证服务 (ADFS) 进行联合身份验证的标识。 由于用户必须要能通过 Azure AD 被发现，因此 Azure 虚拟桌面不支持使用 ADFS 的独立 Active Directory 部署。

## <a name="single-sign-on-sso"></a>单一登录 (SSO)

Azure 虚拟桌面支持[将 Active Directory 联合身份验证服务 (ADFS) 用于 Windows 和 Web 客户端的 SSO](configure-adfs-sso.md)。

否则，想要避免系统提示输入会话主机的凭据，唯一方法是将其保存在客户端中。 建议仅在安全设备上执行此操作，以防其他用户访问你的资源。

## <a name="next-steps"></a>后续步骤

是否想了解其他可确保部署安全性的方法？ 请查看[确保安全的最佳做法](security-guide.md)。
