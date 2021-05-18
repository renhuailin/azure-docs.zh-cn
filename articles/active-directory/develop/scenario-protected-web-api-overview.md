---
title: 受保护的 Web API - 概述
titleSuffix: Microsoft identity platform
description: 了解如何构建受保护的 Web API（概述）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 434c255f0b9634e0d17bad1627ddd92af9498bfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582428"
---
# <a name="scenario-protected-web-api"></a>方案：受保护的 Web API

通过此方案了解如何公开 Web API。 还了解如何保护 Web API，以便只有经过身份验证的用户才能访问它。

若要使用 Web API，可以启用拥有工作和学校帐户且经过身份验证的用户，也可以启用 Microsoft 个人帐户。

## <a name="specifics"></a>详情

下面是保护 Web API 时需要了解的具体信息：

- 应用注册必须至少公开一个范围或一个应用程序角色 。
  - 范围由代表用户调用的 Web API 公开。
  - 应用程序角色由守护程序应用程序调用的 Web API（应用程序代表它们自己调用你的 Web API）公开。
- 如果你创建新的 Web API 应用注册，请选择 `2` 作为 Web API 接受的[访问令牌版本](reference-app-manifest.md#accesstokenacceptedversion-attribute)。 对于旧版 Web API，接受的令牌版本可以是 `null`，但该值是将登录受众限制为仅组织，而不支持个人 Microsoft 帐户 (MSA)。
- Web API 的代码配置必须验证调用 Web API 时使用的令牌。
- 控制器操作中的代码必须验证令牌中的角色或范围。

## <a name="recommended-reading"></a>推荐阅读内容

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章，[应用注册](scenario-protected-web-api-app-registration.md)。
