---
title: 利用应用程序代理构建应用程序访问的复原能力
description: 本指南为架构师和 IT 管理员介绍了如何使用应用程序代理实现本地应用程序访问的复原能力
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724654"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>利用应用程序代理构建应用程序访问的复原能力

应用程序代理是 Azure AD 的一项功能，它使用户能够从远程客户端访问本地 Web 应用程序。 应用程序代理包括在云中的应用程序代理服务和在本地服务器上运行的应用程序代理连接器。 

用户通过经由应用程序代理发布的 URL 访问本地资源。 他们将被重定向到 Azure AD 登录页。 Azure AD 中的应用程序代理服务会将令牌发送到公司网络中的应用程序代理连接器，该连接器会将令牌传递给本地 Active Directory，然后经过身份验证的用户就可以访问本地资源。 下图显示了[连接器组](../manage-apps/application-proxy-connector-groups.md)中的[连接器](../manage-apps/application-proxy-connectors.md)。

> [!IMPORTANT]
> 通过应用程序代理发布应用程序时，必须实现[应用程序代理连接器的容量规划和适当冗余](../manage-apps/application-proxy-connectors.md#capacity-planning)。

![应用程序代理的体系结构关系图](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>如何实现应用程序代理？

若要使用 Azure AD 应用程序代理实现远程访问，请参阅以下资源。

* [计划应用程序代理部署](../manage-apps/application-proxy-deployment-plan.md)

* [高可用性和负载均衡最佳实践](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [配置代理服务器](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [设计可复原的访问控制策略](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [利用凭据管理构建复原能力](resilience-in-credentials.md)

* [使用设备状态构建复原能力](resilience-with-device-states.md)

* [使用连续访问评估 (CAE) 构建复原能力](resilience-with-continuous-access-evaluation.md)

* [构建外部用户身份验证的复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

适用于开发人员的复原能力资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)