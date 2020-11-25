---
title: 使用 Azure AD B2C 的开发人员最佳做法实现复原能力 |Microsoft Docs
description: 通过使用 Azure AD B2C 的客户标识和访问管理中的开发人员最佳做法实现复原能力
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
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919365"
---
# <a name="resilience-through-developer-best-practices"></a>通过开发人员的最佳做法实现复原

在本文中，我们将分享一些基于我们的经验的知识。 你可以在服务的设计和实现中考虑这些建议。

![图像显示开发人员体验组件](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL) 

[Microsoft 身份验证库 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)和[适用于 ASP.NET 的 Microsoft 标识 web 身份验证库](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)简化了应用程序所需的令牌的获取、管理、缓存和刷新。 这些库经过优化，以支持 Microsoft 标识，其中包括改善应用程序复原能力的功能。

开发人员应采用最新版本的 MSAL，并保持最新版本。 请参阅如何在应用程序中 [提高身份验证和授权的复原能力](resilience-app-development-overview.md) 。 如果可能，请避免实现您自己的身份验证堆栈并使用良好构建的库。

## <a name="optimize-directory-reads-and-writes"></a>优化目录读取和写入

Microsoft Azure AD B2C 目录服务一天支持数十亿身份验证。 它设计为每秒的读取速率很高。 优化写入以最大程度地降低依赖关系并提高复原能力。

### <a name="how-to-optimize-directory-reads-and-writes"></a>如何优化目录读取和写入

- **避免在登录时将函数写入目录**：无论是否在自定义策略中) 子句，都不要在不使用前提 (条件的情况下执行写入登录。 需要在登录时进行写入的一个用例是 [用户密码的实时迁移](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration)。 避免在每次登录时都需要写入的任何方案。

  - 用户旅程中的[前置条件](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys)如下所示：

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - [通过与 CAPTCHA 系统集成](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)来构建机器人驱动的登录。

  - 使用 [负载测试示例](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) 模拟注册和登录。 

- **了解限制**：目录同时实现应用程序和租户级别限制规则。 对于读取/获取、写入/发布、更新/PUT 以及删除/删除操作，每个操作都有不同的限制。

  - 登录时，将会在新用户或为现有用户提供的公告下进行一次写入。

  - 在每次登录时创建或更新用户的自定义策略可能会达到应用程序级别 PUT 或 POST rate 限制。 通过 Azure AD 或 Microsoft Graph 更新目录对象时，相同的限制也适用。 同样，检查读取以使每次登录时的读取次数保持最小值。

  - 估计峰值负载以预测目录写入速率并避免限制。 峰值流量估算应包括对注册、登录和多重身份验证 (MFA) 等操作的估计。 确保为高峰流量测试 Azure AD B2C 系统和应用程序。 当下游应用程序或服务不受限制时，Azure AD B2C 可以处理负载，而不会发生限制。

  - 了解并规划迁移时间线。 当计划将用户迁移到使用 Microsoft Graph Azure AD B2C 时，请考虑应用程序和租户限制来计算完成用户迁移所需的时间。 如果使用两个应用程序拆分用户创建作业或脚本，则可以使用每个应用程序的限制。 它仍需保持低于每个租户阈值。

  - 了解迁移作业对其他应用程序的影响。 请考虑由其他依赖应用程序提供的实时流量，以确保不会导致租户级别的限制和你的实时应用程序的资源不足。 有关详细信息，请参阅 [Microsoft Graph 限制指南](https://docs.microsoft.com/graph/throttling)。
  
## <a name="extend-token-lifetimes"></a>扩展令牌生存期

在极少数情况下，当 Azure AD B2C 身份验证服务无法完成新的登录和登录时，你仍然可以为已登录的用户提供缓解措施。 使用 [配置](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)，您可以允许已登录的用户继续使用该应用程序，而无需任何明显的中断，直到用户从应用程序中注销或 [会话](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) 由于不活动而超时。

你的业务要求和所需的最终用户体验将要求 Spa)  (的 web 和单页应用程序的令牌刷新频率。

### <a name="how-to-extend-token-lifetimes"></a>如何扩展令牌生存期

- **Web 应用程序**：对于在登录开始时验证身份验证令牌的 web 应用程序，该应用程序依赖于会话 cookie 来继续扩展会话有效性。

  - 允许用户通过实施将继续基于用户活动续订会话的滚动会话时间，保持登录。 如果存在长期颁发中断，则这些会话时间可能会进一步增加，作为应用程序的一次性配置。 将会话的生存期保持为允许的最大值。

- **Spa**： SPA 可能依赖于访问令牌来调用 api。 SPA 一般使用不会导致刷新令牌的隐式流。 如果浏览器仍具有与 Azure AD B2C 的活动会话，SPA 可以使用隐藏的 iframe 对授权终结点执行新的令牌请求。 对于 Spa，有几个选项可用于允许用户继续使用该应用程序。

  - 扩展访问令牌的有效期，以满足你的业务需求。

  - 构建应用程序，以使用 API 网关作为身份验证代理。 在此配置中，无需任何身份验证的 SPA 加载以及对 API 网关的 API 调用。 API 网关使用基于策略的 [授权代码授予](https://oauth.net/2/grant-types/authorization-code/) 来发送用户，并对用户进行身份验证。 然后，将使用身份验证 cookie 维护 API 网关和客户端之间的身份验证会话。 使用 api 网关或其他一些直接身份验证方法（如证书、客户端凭据或 API 密钥）获取的令牌，从 API 网关提供 Api。

  - 将[你的 SPA 从隐式授权迁移](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/)到[授权代码授予流](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application)，并将代码交换 (PKCE) 和跨域资源共享 (CORS) 支持。 将你的应用程序从 MSAL.js 1.x 迁移到 MSAL.js 2.x 以实现 Web 应用程序的复原能力。

  - 对于移动应用程序，建议同时扩展刷新和访问令牌生存期。

- **后端或微服务应用程序**：由于后端 (后台程序) 应用程序是非交互的且不在用户上下文中，令牌被盗的目标方大大降低。 建议在安全和生存期之间进行平衡，并设置长令牌生存期。

## <a name="configure-single-sign-on"></a>配置单一登录

使用 [单一登录 (SSO) ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)，用户可以使用单个帐户登录一次，并可以访问多个应用程序。 无论平台或域名如何，应用程序都可以是 web 应用、移动应用或单页面应用程序 (SPA) 。 用户首次登录应用程序时，Azure AD B2C 会保留 [基于 cookie 的会话](https://docs.microsoft.com/azure/active-directory-b2c/session-overview)。

在后续身份验证请求中，Azure AD B2C 读取和验证基于 cookie 的会话，并颁发访问令牌，而不提示用户重新登录。 如果在策略或应用程序中使用有限范围配置 SSO，以后访问其他策略和应用程序时将需要全新身份验证。

### <a name="how-to-configure-sso"></a>如何配置 SSO

将[SSO 配置](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start)为租户范围 (默认) ，以允许租户中的多个应用程序和用户流共享相同的用户会话。 租户范围内的配置为全新身份验证提供大多数复原能力。  

## <a name="safe-deployment-practices"></a>安全部署实践

最常见的服务 disrupters 是代码和配置更改。 采用持续集成和持续交付 (CICD) 进程和工具有助于大规模快速部署，并减少在测试和部署到生产期间发生的人为错误。 采用 CICD 来减少错误、效率和一致性。 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) 是 CICD 的一个示例。

## <a name="web-application-firewall"></a>Web 应用程序防火墙

保护应用程序免受已知漏洞的威胁，如分布式拒绝服务 (DDoS) 攻击、SQL 注入、跨站点脚本、远程代码执行等，如 [OWASP Top 10](https://owasp.org/www-project-top-ten/)中所述。 部署 Web 应用程序防火墙 (WAF) 可以抵御常见的攻击和漏洞。

- 使用 Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview)，它提供集中式防护来防范攻击。

- 使用带有 Azure AD [Identity protection 和条件访问](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) 的 WAF 在使用 Azure AD B2C 时提供多层保护。  

## <a name="secrets-rotation"></a>机密轮换

Azure AD B2C 对应用程序、Api、策略和加密使用机密。 机密安全身份验证、外部交互和存储。 美国国家标准与技术研究院 (NIST) 会调用时间跨度，在此期间，特定密钥有权由合法实体 cryptoperiod。 选择适当的 [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) ，以满足你的业务需求。 开发人员需要在过期之前手动设置过期和旋转密码。

### <a name="how-to-implement-secret-rotation"></a>如何实现秘密旋转

- 将 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 用于支持的资源，以便向支持 Azure AD 身份验证的任何服务进行身份验证。 使用托管标识时，可以自动管理资源，包括轮换凭据。

- 清点 Azure AD B2C 中配置的所有 [密钥和证书](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) 。 此列表可能包括用于 SAML 的自定义策略、 [api](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api)、签名 ID 令牌和证书中使用的密钥。

- 使用 CICD，将在两个月内过期的机密旋转到预计的高峰季节。 与证书关联的私钥的建议最大 cryptoperiod 为一年。

- 主动监视和旋转 API 访问凭据，如密码和证书。

## <a name="test-rest-apis"></a>测试 REST Api

在复原环境中，REST Api 的测试需要包括对 HTTP 代码、响应负载、标头和性能的验证。 测试不应只包含很高兴的路径测试，还应检查 API 是否正常处理问题方案。

### <a name="how-to-test-apis"></a>如何测试 Api

建议你的测试计划包括 [综合性 API 测试](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing)。 如果要规划即将发生的冲击，因为促销或假期流量，则需要使用新估计来修改负载测试。 在开发人员环境而不是生产环境中 (CDN) 执行 Api 和内容交付网络的负载测试。

## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原资源](resilience-b2c.md)
  - [复原最终用户体验](resilient-end-user-experience.md)
  - [具有外部进程的弹性接口](resilient-external-processes.md)
  - [通过监视和分析进行恢复](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在应用程序中提高身份验证和授权的复原能力](resilience-app-development-overview.md)
