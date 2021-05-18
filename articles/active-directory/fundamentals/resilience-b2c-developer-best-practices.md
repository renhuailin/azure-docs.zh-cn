---
title: 使用 Azure AD B2C 通过开发人员最佳做法实现复原能力 | Microsoft Docs
description: 使用 Azure AD B2C 在客户标识和访问管理中通过开发人员最佳做法实现复原能力
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724919"
---
# <a name="resilience-through-developer-best-practices"></a>通过开发人员最佳做法实现复原能力

在本文中，我们将分享一些经验教训，它们基于我们与大客户合作的体验。 你可以在服务的设计和实现中考虑这些建议。

![图像显示了开发人员体验组件](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL)

[Microsoft 身份验证库 (MSAL)](../develop/msal-overview.md) 和[适用于 ASP.NET 的 Microsoft 标识 Web 身份验证库](../develop/reference-v2-libraries.md)简化了应用程序所需令牌的获取、管理、缓存和刷新。 这些库经过专门优化，可支持 Microsoft 标识，其中包括可改进应用程序复原能力的功能。

开发人员应采用最新版本的 MSAL 并了解最新信息。 请参阅[如何提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)。 如果可能，请避免实现自己的身份验证堆栈，使用构建良好的库。

## <a name="optimize-directory-reads-and-writes"></a>优化目录读取和写入

Microsoft Azure AD B2C 目录服务支持一天进行数十亿次身份验证。 其设计可实现较高的每秒读取速率。 优化写入以最大程度地减少依赖关系并提高复原能力。

### <a name="how-to-optimize-directory-reads-and-writes"></a>如何优化目录读取和写入

- 避免在登录时将函数写入目录：切勿在自定义策略中执行无前提条件（if 子句）的登录时写入。 需要进行登录时写入的一个用例是[用户密码的实时迁移](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)。 避免任何在每次登录时都需要写入的方案。

  - 用户旅程中的[前提条件](../../active-directory-b2c/userjourneys.md)类似于下面这样：

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - [通过与 CAPTCHA 系统集成](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)，阻止机器人驱动的登录。

  - 使用[负载测试示例](../../active-directory-b2c/best-practices.md#testing)模拟注册和登录。 

- 了解限制：目录实现应用程序和租户级别限制规则。 Read/GET、Write/POST、Update/PUT 和 Delete/DELETE 操作有进一步的速率限制，并且每个操作有不同的限制。

  - 登录时的写入对于新用户属于 POST，对于现有用户属于 PUT。

  - 在每次登录时创建或更新用户的自定义策略可能会达到应用程序级别 PUT 或 POST 速率限制。 通过 Azure AD 或 Microsoft Graph 更新目录对象时，会应用相同的限制。 同样，检查读取以使每次登录时的读取次数保持最少。

  - 估计峰值负载以预测目录写入速率并避免限制。 峰值流量估计应包括对诸如注册、登录和多重身份验证 (MFA) 等操作的估计。 确保针对峰值流量测试 Azure AD B2C 系统和应用程序。 可能存在这种情况下：Azure AD B2C 可以不受限制地处理负载，而下游应用程序或服务无法这样做。

  - 了解并规划迁移时间线。 当计划使用 Microsoft Graph 将用户迁移到 Azure AD B2C 时，请考虑应用程序和租户限制以计算完成用户迁移所需的时间。 如果使用两个应用程序拆分用户创建作业或脚本，则可以使用每个应用程序限制。 它仍需保持低于每个租户阈值。

  - 了解迁移作业对其他应用程序的影响。 考虑由其他依赖应用程序提供的实时流量，以确保不会在租户级别造成限制，并且不会导致实时应用程序资源不足。 有关详细信息，请参阅 [Microsoft Graph 限制指导](/graph/throttling)。
  
## <a name="extend-token-lifetimes"></a>延长令牌生存期

在极少数情况下，当 Azure AD B2C 身份验证服务无法完成新的注册和登录时，你仍然可以为已登录的用户提供缓解措施。 借助[配置](../../active-directory-b2c/configure-tokens.md)，可以允许已登录的用户继续使用应用程序，而没有任何明显的中断，直到用户从应用程序中注销或[会话](../../active-directory-b2c/session-behavior.md)由于处于不活动状态而超时。

业务要求和所需的最终用户体验将决定你为 Web 和单页应用程序 (SPA) 进行令牌刷新的频率。

### <a name="how-to-extend-token-lifetimes"></a>如何延长令牌生存期

- **Web 应用程序**：对于在登录开始时验证身份验证令牌的 Web 应用程序，应用程序依赖于会话 Cookie 来继续扩展会话有效性。

  - 通过实现基于用户活动持续续订会话的滚动会话时间，使用户可以保持登录状态。 如果存在长期令牌颁发中断，则这些会话时间可以作为应用程序中的一次性配置而进一步增加。 使会话的生存期保持为允许的最大值。

- SPA：SPA 可能依赖于访问令牌来调用 API。 在传统上，SPA 使用不会导致刷新令牌的隐式流。 如果浏览器仍与 Azure AD B2C 保持活动会话，则 SPA 可以使用隐藏 iframe 对授权终结点执行新的令牌请求。 对于 SPA，有几个选项可用于允许用户持续使用应用程序。

  - 延长访问令牌的有效期，以满足业务需求。

  - 构建应用程序，以使用 API 网关作为身份验证代理。 在此配置中，SPA 加载时不进行任何身份验证，会对 API 网关进行 API 调用。 API 网关使用基于策略的[授权代码授予](https://oauth.net/2/grant-types/authorization-code/)，通过登录过程发送用户，并对用户进行身份验证。 随后使用身份验证 Cookie 维护 API 网关与客户端之间的身份验证会话。 使用 API 网关获取的令牌或其他一些直接身份验证方法（如证书、客户端凭据或 API 密钥），从 API 网关为 API 提供服务。

  - 借助用于代码交换的证明密钥 (PKCE) 和跨域资源共享 (CORS) 支持，[将 SPA 从隐式授予迁移](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/)到[授权代码授予流](../../active-directory-b2c/implicit-flow-single-page-application.md)。 将应用程序从 MSAL.js 1.x 迁移到 MSAL.js 2.x 以实现 Web 应用程序的复原能力。

  - 对于移动应用程序，建议同时延长刷新和访问令牌生存期。

- 后端或微服务应用程序：由于后端（守护程序）应用程序是非交互式的，不在用户上下文中，因此令牌失窃的可能性会大幅降低。 建议在安全性与生存期之间达到平衡，并设置较长的令牌生存期。

## <a name="configure-single-sign-on"></a>配置单一登录

借助[单一登录 (SSO)](../manage-apps/what-is-single-sign-on.md)，用户可以使用单个帐户登录一次，然后即可访问多个应用程序。 应用程序可以是 Web、移动或单页应用程序 (SPA)，不管它们的平台或域名如何。 当用户最初登录到应用程序时，Azure AD B2C 会保留一个[基于 Cookie 的会话](../../active-directory-b2c/session-behavior.md)。

收到后续的身份验证请求后，Azure AD B2C 会读取并验证该基于 Cookie 的会话，然后颁发访问令牌，且不提示用户重新登录。 如果在策略或应用程序中将 SSO 配置为有限的范围，那么以后访问其他策略和应用程序时将需要全新的身份验证。

### <a name="how-to-configure-sso"></a>如何配置 PRO

[将 SSO 配置](../hybrid/how-to-connect-sso-quick-start.md)为租户范围（默认），以允许租户中的多个应用程序和用户流共享相同的用户会话。 租户范围的配置为全新身份验证提供最大的复原能力。  

## <a name="safe-deployment-practices"></a>安全部署实践

最常见的服务中断因素是代码和配置更改。 采用持续集成和持续交付 (CICD) 过程和工具有助于大规模快速部署，并减少在测试和部署到生产环境过程中的人为错误。 采用 CICD 来减少错误，提高效率和一致性。 [Azure Pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) 是 CICD 的一个示例。

## <a name="web-application-firewall"></a>Web 应用程序防火墙

保护应用程序免受已知漏洞的威胁，如分布式拒绝服务 (DDoS) 攻击、SQL 注入、跨站脚本、远程代码执行和 [OWASP Top 10](https://owasp.org/www-project-top-ten/) 中所述的许多其他漏洞。 部署 Web 应用程序防火墙 (WAF) 可以抵御常见的攻击和漏洞。

- 使用 Azure [WAF](../../web-application-firewall/overview.md)，它提供集中式保护来防范攻击。

- 在使用 Azure AD B2C 时，将 WAF 与 Azure AD [标识保护和条件访问一起使用以提供多层保护](../../active-directory-b2c/conditional-access-identity-protection-overview.md)。  

## <a name="secrets-rotation"></a>机密轮换

Azure AD B2C 对应用程序、API、策略和加密使用机密。 机密可保护身份验证、外部交互和存储。 美国国家标准与技术研究所 (NIST) 将授权特定密钥供合法实体使用的时间跨度称为加密期。 选择合适的[cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)长度以满足业务需求。 开发人员需要手动设置到期并在机密到期之前进行轮换。

### <a name="how-to-implement-secret-rotation"></a>如何实现机密轮换

- 使用受支持资源的[托管标识](../managed-identities-azure-resources/overview.md)向支持 Azure AD 身份验证的任何服务验证身份。 使用托管标识时，可以自动管理资源，包括凭据轮换。

- 清点 Azure AD B2C 中[配置的所有密钥和证书](../../active-directory-b2c/policy-keys-overview.md)。 此列表可能包括的自定义策略、[API](../../active-directory-b2c/secure-rest-api.md)、签名 ID 令牌和 SAML 证书中使用的密钥。

- 使用 CICD，可轮换将在预期旺季两个月内到期的机密。 与证书关联的私钥的建议最大加密期为一年。

- 主动监视和轮换 API 访问凭据，如密码和证书。

## <a name="test-rest-apis"></a>测试 REST API

在复原能力上下文中，REST API 的测试需要包括 HTTP 代码、响应有效负载、标头和性能的验证。 测试不应只包含快乐路径测试，还应检查 API 是否可正常处理问题情景。

### <a name="how-to-test-apis"></a>如何测试 API

建议测试计划包括[综合 API 测试](../../active-directory-b2c/best-practices.md#testing)。 如果为即将到来的流量激增（由于促销或假日流量）进行规划，则需要使用新估计来修订负载测试。 在开发人员环境而不在生产环境中执行 API 和内容交付网络 (CDN) 的负载测试。

## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原能力资源](resilience-b2c.md)
  - [可复原的最终用户体验](resilient-end-user-experience.md)
  - [与外部进程建立可复原接口](resilient-external-processes.md)
  - [通过监视和分析实现复原能力](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)