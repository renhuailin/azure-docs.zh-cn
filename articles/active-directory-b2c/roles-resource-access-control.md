---
title: 角色和资源访问控制
titleSuffix: Azure AD B2C
description: 了解如何使用角色来控制资源访问。
services: active-directory-b2c
author: kengaderdus
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: b9a4e9cf2fa8c8cd91fed738ec14e88d18063044
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620836"
---
# <a name="roles-and-resource-access-control"></a>角色和资源访问控制

在规划访问控制策略时，最好为用户分配访问资源所需的最低特权角色。 下表介绍了 Azure AD B2C 租户中的主要资源，以及最适合管理这些资源的用户的管理角色。

|资源  |说明  |角色  |
|---------|---------|---------|
|[应用程序注册](tutorial-register-applications.md) | 在 Azure AD B2C 中创建和管理 Web、移动和本机应用程序注册的所有方面。|[应用程序管理员](../active-directory/roles/permissions-reference.md#application-administrator)|
|[标识提供者](add-identity-provider.md)| 配置[本地标识提供者](identity-provider-local.md)和外部社交或企业标识提供者。 | [外部标识提供者管理员](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API 连接器](add-api-connector.md)| 将用户流与 Web API 集成，以自定义用户体验，并与外部系统集成。|[外部 ID 用户流管理员](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[公司品牌](customize-ui.md#configure-company-branding)| 自定义用户流页面。| [全局管理员](../active-directory/roles/permissions-reference.md#global-administrator)|
|[用户属性](user-flow-custom-attributes.md)| 添加或删除适用于所有用户流的自定义属性。| [外部 ID 用户流属性管理员](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|管理用户| 管理[使用者帐户](manage-users-portal.md)和管理帐户，如本文中所述。| [用户管理员](../active-directory/roles/permissions-reference.md#user-administrator)|
|角色和管理员| 管理 Azure AD B2C 目录中的角色分配。 创建和管理可被分配给 Azure AD B2C 角色的组。 |[全局管理员](../active-directory/roles/permissions-reference.md#global-administrator)、[特权角色管理员](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[用户流](user-flow-overview.md)|可以快速配置和启用常见标识任务，例如注册、登录和配置文件编辑。| [外部 ID 用户流管理员](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[自定义策略](user-flow-overview.md)| 在 Azure AD B2C 中创建、读取、更新和删除所有自定义策略。| [B2C IEF 策略管理员](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[策略密钥](policy-keys-overview.md)|添加和管理用于签名和验证自定义策略中使用的令牌、客户端密码、证书和密码的加密密钥。|[B2C IEF 密钥集管理员](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|