---
title: Windows 虚拟桌面身份验证-Azure
description: Windows 虚拟桌面的身份验证方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04a4366bfee6b1d9c5f52d649910163269962684
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709251"
---
# <a name="supported-authentication-methods"></a>支持的身份验证方法

本文简要概述了可在 Windows 虚拟桌面中使用的身份验证类型。

## <a name="session-host-authentication"></a>会话主机身份验证

Windows 虚拟桌面支持 NT LAN Manager (NTLM) ，以及用于会话主机身份验证的 Kerberos。 但是，若要使用 Kerberos，客户端需要从域控制器上运行的密钥发行中心 (KDC) 服务获取 Kerberos 安全票证。 若要获取票证，客户端需要向域控制器直接显示一行。 你可以通过使用企业网络直接了解这一行为。 你还可以使用与企业网络的 VPN 连接或设置 [KDC 代理服务器](key-distribution-center-proxy.md)。

以下是当前支持的登录方法：

- Windows 桌面客户端
    - 用户名和密码
    - 智能卡
    - Windows Hello 企业版 (仅限证书信任) 
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
>智能卡和 Windows Hello 企业版只能使用 Kerberos 登录。 使用 Kerberos 登录需要向域控制器或 [KDC 代理服务器](key-distribution-center-proxy.md)提供线路。

## <a name="hybrid-identity"></a>混合标识

Windows 虚拟桌面通过 Azure Active Directory (AD) （包括使用 Active Directory 联合身份验证服务 (ADFS) 的联合身份验证）支持 [混合标识](../active-directory/hybrid/whatis-hybrid-identity.md) 。 由于用户必须通过 Azure AD 发现，因此 Windows 虚拟桌面不支持使用 ADFS 的独立 Active Directory 部署。

## <a name="single-sign-on-sso"></a>单一登录 (SSO)

Windows 虚拟桌面目前不支持 Active Directory 联合身份验证服务 (ADFS) 用于 SSO。

避免系统提示输入会话主机凭据的唯一方法是将其保存在客户端中。 建议仅对安全设备执行此操作，以防其他用户访问资源。

## <a name="next-steps"></a>后续步骤

您是否想了解用于确保部署安全的其他方法？ 查看 [最佳安全方案](security-guide.md)。
