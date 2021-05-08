---
title: Azure AD B2C 服务限制和局限性
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 服务的服务限制和局限性参考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 190d88e62069a34b61017a0079f75696d67f6c82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979906"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Azure Active Directory B2C 服务限制和局限性

本文介绍 Azure Active Directory B2C (Azure AD B2C) 服务的使用限制和其他服务限制。

## <a name="end-userconsumption-related-limits"></a>最终用户/消耗相关限制

以下最终用户相关服务限制适用于 Azure AD B2C 支持的所有身份验证和授权协议，包括 SAML、Open ID Connect、OAuth2 和 ROPC。

|类别 |限制    |
|---------|---------|
|每个 Azure AD B2C 租户的每个 IP 地址的请求数       |6,000/5 分钟          |
|每个 Azure AD B2C 租户的请求总数     |12,000/分钟          |

请求数可根据在 Azure AD B2C 用户旅程期间发生的目录读取和写入数而变化。 例如，从目录中读取的简单登录旅程包含 1 个请求。 如果登录旅程还必须更新目录，则此操作将被计为其他请求。

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C 配置限制

下表列出了 Azure AD B2C 服务中的管理配置限制。

|类别  |限制  |
|---------|---------|
|每个应用程序的作用域数        |1000          |
| 每个用户的[自定义属性](user-profile-attributes.md#extension-attributes)数 <sup>1</sup>       |100         |
|每个应用程序的重定向 URL 数       |100         |
|每个应用程序的注销 URL 数        |1          |
|每个属性的字符串限制      |250 个字符          |
|每个订阅的 B2C 租户数      |20         |
|自定义策略中的[继承](custom-policy-overview.md#inheritance-model)级别     |10         |
|每个 Azure AD B2C 租户的策略数      |200          |
|最大策略文件大小      |400 KB          |

<sup>1</sup> 另请参阅 [Azure AD 服务限制和局限性](../active-directory/enterprise-users/directory-service-limits-restrictions.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Microsoft Graph 的限制指南](/graph/throttling) 
- 了解 [Azure AD B2C 应用程序的验证差异](../active-directory/develop/supported-accounts-validation.md)













