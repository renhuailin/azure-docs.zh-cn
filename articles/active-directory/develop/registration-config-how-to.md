---
title: 获取 Azure AD 应用注册的终结点
titleSuffix: Microsoft identity platform
description: 如何使用 Azure AD 查找要开发或注册的自定义应用程序的身份验证终结点。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 6d557a35c9e98a8941a94d5871f578101d935580
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153741"
---
# <a name="how-to-discover-endpoints"></a>如何发现终结点

可以在 [Azure 门户](https://portal.azure.com)中找到应用程序的身份验证终结点。

1. 登录到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 选择“Azure Active Directory” 。
1. 在“管理”下，选择“应用注册”，然后在顶部菜单中选择“终结点”。

    此时会显示“终结点”页，其中显示租户的身份验证终结点。
    
    将与所用身份验证协议匹配的终结点与“应用程序(客户端) ID”结合使用，生成特定于应用程序的身份验证请求。

“国家云”（例如 Azure AD 中国、德国和美国政府）有自己的应用注册门户和 Azure AD 身份验证终结点。 有关详细信息，请参阅[国家云概述](authentication-national-cloud.md)。

## <a name="next-steps"></a>后续步骤

有关不同 Azure 环境中的终结点的详细信息，请参阅[国家云概述](authentication-national-cloud.md)。
