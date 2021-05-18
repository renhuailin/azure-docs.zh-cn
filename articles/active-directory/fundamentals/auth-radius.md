---
title: 通过 Azure Active Directory 进行 RADIUS 身份验证
description: 有关如何使用 Azure Active Directory 实现 RADIUS 身份验证的体系结构指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168621"
---
# <a name="radius-authentication-with-azure-active-directory"></a>通过 Azure Active Directory 进行 RADIUS 身份验证

远程身份验证拨入用户服务 (RADIUS) 是一种网络协议，通过对拨入用户启用集中式身份验证和授权来保护网络。 许多应用程序仍然依赖于 RADIUS 协议来对用户进行身份验证。

Microsoft Windows Server 具有名为网络策略服务器 (NPS) 的角色，该角色可充当 RADIUS 服务器并支持 RADIUS 身份验证。

Azure Active Directory (Azure AD) 通过基于 RADIUS 的系统实现多重身份验证。 如果客户想要将 Azure AD 多重身份验证应用到前面提到的任何 RADIUS 工作负载，则他们可以在其 Windows NPS 服务器上安装 Azure AD 多重身份验证 NPS 扩展。 

Windows NPS 服务器会根据 Active Directory 对用户的凭据进行身份验证，然后将多重身份验证请求发送到 Azure。 然后，用户在其移动身份验证器上收到质询。 成功后，将允许客户端应用程序连接到该服务。 

## <a name="use-when"></a>何时使用： 

需要向某些应用程序添加多重身份验证，如
* 虚拟专用网络 (VPN)
* WiFi 访问
* 远程桌面网关 (RDG)
* 虚拟桌面基础结构 (VDI)
* 依赖于 RADIUS 协议对服务中的用户进行身份验证的任何其他应用程序。 

> [!NOTE]
> 建议将 VPN 升级为 SAML，并使用 Azure AD 直接联合 VPN，而不是依靠 RADIUS 和 Azure AD 多重身份验证 NPS 扩展将 Azure AD 多重身份验证应用于 VPN 工作负载。 这为你的 VPN 提供了全面的 Azure AD 保护，包括条件访问、多重身份验证、设备兼容性和标识保护。

![体系结构图](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>系统的组件 

* **客户端应用程序 (VPN 客户端)** ：向 RADIUS 客户端发送身份验证请求。

* **RADIUS 客户端**：转换来自客户端应用程序的请求，并将其发送到安装了 NPS 扩展的 RADIUS 服务器。

* **RADIUS 服务器**：连接 Active Directory 以执行 RADIUS 请求的主要身份验证。 成功后，会将请求传递到 Azure AD 多重身份验证 NPS 扩展。

* **NPS 扩展**：触发一个 Azure AD 多重身份验证请求来执行辅助身份验证。 如果成功，NPS 扩展将向 RADIUS 服务器提供包含由 Azure 的安全令牌服务颁发的安全令牌（其中包含多重身份验证声明），从而完成身份验证请求。

* **Azure AD 多重身份验证**：与 Azure AD 通信，检索用户的详细信息并使用配置给用户的验证方法执行辅助身份验证。

## <a name="implement-radius-with-azure-ad"></a>通过 Azure AD 实现 RADIUS 

* [使用 NPS 提供 Azure AD 多重身份验证功能](../authentication/howto-mfa-nps-extension.md) 

* [配置 Azure AD 多重身份验证 NPS 扩展](../authentication/howto-mfa-nps-extension-advanced.md) 

* [使用 NPS 扩展进行 Azure AD 多重身份验证的 VPN](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

