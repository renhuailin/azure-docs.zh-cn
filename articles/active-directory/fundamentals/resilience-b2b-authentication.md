---
title: 在 Azure Active Directory 中通过外部用户身份验证构建复原能力
description: 面向 IT 管理员和架构师为外部用户构建复原身份验证的指南
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
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919366"
---
# <a name="build-resilience-in-external-user-authentication"></a>在外部用户身份验证中构建复原能力

[AZURE ACTIVE DIRECTORY b2b 协作](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD b2b) 是 [外部标识](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) 的一项功能，可与其他组织和个人协作。 它可让来宾用户安全地加入到 Azure AD 租户中，而无需管理其凭据。 外部用户使用外部标识提供者提供的标识和凭据 (IdP) ，因此他们无需记住新的凭据。 

## <a name="ways-to-authenticate-external-users"></a>对外部用户进行身份验证的方法

你可以选择对目录的外部用户身份验证方法。 你可以使用 Microsoft Idp 或其他 Idp。

对于每个外部 IdP，都依赖于该 IdP 的可用性。 通过一些连接到 Idp 的方法，可以执行一些操作来提高恢复能力。

> [!NOTE] 
> Azure AD B2B 提供内置功能，可对任何 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) 租户中的任何用户进行身份验证，也可以使用个人 [Microsoft 帐户](https://account.microsoft.com/account)进行身份验证。 不需要使用这些内置选项进行任何配置。

### <a name="considerations-for-resilience-with-other-idps"></a>与其他 Idp 的恢复性注意事项

使用 external Idp 进行来宾用户身份验证时，必须确保保持维护，以防中断。

| 身份验证方法| 复原注意事项 |
| - | - |
| 与 [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) 或 [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation)等社交 idp 的联合。| 你必须通过 IdP 维护你的帐户，并配置你的客户端 ID 和客户端密码。 |
| [与 SAML 和 WS-Federation 标识提供者的直接联合](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| 必须与 IdP 所有者合作，以便访问其终结点。 <br>必须维护包含证书和终结点的元数据。 |
| [电子邮件一次性密码](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| 通过此方法，你依赖于 Microsoft 的电子邮件系统、用户的电子邮件系统和用户的电子邮件客户端。 |


 

## <a name="self-service-sign-up-preview"></a>自助注册 (预览版) 

作为发送邀请或链接的替代方法，您可以启用 [自助服务注册](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview)。  这允许外部用户请求对应用程序的访问权限。 必须创建 [API 连接器](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) 并将其与用户流相关联。 将定义用户体验的用户流与一个或多个应用程序相关联。 

可以使用 [API 连接器](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) 将自助服务注册用户流与外部系统的 api 集成。 此 API 集成可用于 [自定义批准工作流](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals)、 [执行身份验证](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)和其他任务，如覆盖用户属性。 使用 Api 需要你管理以下依赖项。

* **API 连接器身份验证**：设置连接器需要使用终结点 URL、用户名和密码。 设置用于维护这些凭据的进程，并与 API 所有者合作，以确保你知道任何到期计划。

* **Api 连接器响应**：如果 api 不可用，则在注册流中设计 api 连接器以正常失败。 检查并向 API 开发人员提供这些 [示例 API 响应](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) 和 [故障排除的最佳实践](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector)。 与 API 开发团队合作，测试所有可能的响应方案，包括延续、验证错误和阻塞响应。 

## <a name="next-steps"></a>后续步骤
适用于管理员和架构师的复原资源
 
* [通过凭据管理构建复原能力](resilience-in-credentials.md)

* [用设备状态生成复原能力](resilience-with-device-states.md)

* [使用持续存取评估 (CAE) 生成复原能力 ](resilience-with-continuous-access-evaluation.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)

面向开发人员的复原资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
 
