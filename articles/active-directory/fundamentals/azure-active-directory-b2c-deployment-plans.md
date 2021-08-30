---
title: Azure AD B2C 部署
description: Azure Active Directory B2C 部署指南
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 7/12/2021
ms.author: gasinh
author: gargi-sinha
manager: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dafaa6174826259eaee1c6d0b290b4de6b63911
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724820"
---
# <a name="azure-active-directory-b2c-deployment-plans"></a>Azure Active Directory B2C 部署计划

Azure Active Directory B2C 是一种可缩放的标识和访问管理解决方案。 其高度灵活性可满足你的业务期望，并能与现有基础结构顺利集成，实现进一步的数字化。

为了帮助组织了解业务要求并遵守合规性边界，建议在整个 Azure Active Directory (Azure AD) B2C 部署中采用循序渐进的方法。

| 功能 | 说明 |
|:-----|:------|
| [规划](#plan-an-azure-ad-b2c-deployment) | 准备好要部署的 Azure AD B2C 项目。 首先确定利益干系人，然后定义项目时间线。 |
| [实现](#implement-an-azure-ad-b2c-deployment) | 首先启用身份验证和授权，然后执行完整的应用程序载入。 |
| [监视](#monitor-an-azure-ad-b2c-solution) | Azure AD B2C 解决方案到位后，启用日志记录、审核和报告。 |

## <a name="plan-an-azure-ad-b2c-deployment"></a>规划 Azure AD B2C 部署

此阶段包括以下功能：

| 功能 | 说明 |
|:------------|:------------|
|[业务要求评审](#business-requirements-review) | 评估组织的状况和期望 |
| [利益干系人](#stakeholders) |建立项目团队 |
|[通信](#communication) | 与团队就项目进行沟通 |
| [时间线](#timeline) | 关键项目里程碑提醒  |

### <a name="business-requirements-review"></a>业务要求评审

- 评估关闭现有系统并[迁移到 Azure AD B2C](../../active-directory-b2c/overview.md) 的主要原因。

- 对于新应用程序，请[规划和设计](../../active-directory-b2c/best-practices.md#planning-and-design)客户标识访问管理 (CIAM) 系统

- 确定客户的位置并[在相应的数据中心创建租户](../../active-directory-b2c/tutorial-create-tenant.md)。

- 检查你拥有的应用程序类型
  - 检查当前支持的平台 - [MSAL](../develop/msal-overview.md) 或[开源](https://azure.microsoft.com/free/open-source/search/?OCID=AID2200277_SEM_f63bcafc4d5f1d7378bfaa2085b249f9:G:s&ef_id=f63bcafc4d5f1d7378bfaa2085b249f9:G:s&msclkid=f63bcafc4d5f1d7378bfaa2085b249f9)。
  - 对于后端服务，请使用[客户端凭据流](../develop/msal-authentication-flows.md#client-credentials)。

- 如果你打算从现有标识提供者 (IdP) 进行迁移

  - 请考虑使用[无缝迁移方法](../../active-directory-b2c/user-migration.md#seamless-migration)
  - 了解[如何迁移现有应用程序](https://github.com/azure-ad-b2c/user-migration)
  - 确保同时存在多种解决方案。

- 确定要使用的协议

  - 如果当前正在使用 Kerberos、NTLM 和 WS-Fed，请[迁移并重构应用程序](https://www.bing.com/videos/search?q=application+migration+in+azure+ad+b2c&docid=608034225244808069&mid=E21B87D02347A8260128E21B87D02347A8260128&view=detail&FORM=VIRE)。 迁移后，应用程序就可以支持新式标识协议，例如 OAuth 2.0 和 OpenID Connect (OIDC)，以实现进一步的标识保护和安全性。

### <a name="stakeholders"></a>利益干系人

如果技术项目失败，通常是由于在影响、结果和责任方面不符合预期而导致的。 为了避免这些问题，请[确保让合适的利益干系人参与](./active-directory-deployment-plans.md#include-the-right-stakeholders)，并确保利益干系人了解其角色。

- 确定应用程序的主要架构师、项目经理和所有者。

- 考虑提供通讯组列表 (DL)。 使用此 DL，你可以与 Microsoft 帐户团队或工程部门沟通产品问题。 你可以提出问题并接收重要通知。

- 确定组织外可以为你提供支持的合作伙伴或资源。

### <a name="communication"></a>通信

沟通对于任何新服务的成功都至关重要。 主动向用户传达更改事宜。 及时告知用户他们的体验将如何变化、何时变化以及在遇到问题时如何获取支持。

### <a name="timeline"></a>时间线

定义明确的期望并跟进计划以实现关键里程碑：

- 预计试点日期

- 预计启动日期

- 任何可能影响项目交付日期的日期

## <a name="implement-an-azure-ad-b2c-deployment"></a>实施 Azure AD B2C 部署

此阶段包括以下功能：

| 功能 | 说明 |
|:-------------|:--------------|
| [部署身份验证和授权](#deploy-authentication-and-authorization) | 了解[身份验证和授权](../develop/authentication-vs-authorization.md)方案 |
| [部署应用程序和用户标识](#deploy-applications-and-user-identities) | 计划部署客户端应用程序和迁移用户标识  |
| [客户端应用程序载入和可交付结果](#client-application-onboarding-and-deliverables) | 载入客户端应用程序并测试解决方案 |
| [安全性](#security) | 提高标识解决方案的安全性 |
|[遵从性](#compliance) | 满足法规要求 |
|[用户体验](#user-experience) | 启用用户友好的服务 |

### <a name="deploy-authentication-and-authorization"></a>部署身份验证和授权

- 首先[设置 Azure AD B2C 租户](../../active-directory-b2c/tutorial-create-tenant.md)。

- 对于业务驱动的授权，请使用 [Azure AD B2C Identity Experience Framework (IEF) 示例用户旅程](https://github.com/azure-ad-b2c/samples#local-account-policy-enhancements)

- 尝试使用 [Open Policy Agent](https://www.openpolicyagent.org/)。

若要详细了解 Azure AD B2C，请参阅[此开发人员课程](https://aka.ms/learnaadb2c)。

按照以下示例清单获取更多指导：

- 确定需要访问应用程序的不同角色。  

- 定义当前在现有系统中管理权限和权利的方式，以及如何规划未来。

- 检查是否有权限存储以及是否有任何需要添加到目录中的权限。

- 如果需要委派管理，请定义如何解决。 例如，客户的客户管理。

- 检查应用程序是否直接调用 API 管理器 (APIM)。 在向应用程序颁发令牌之前，可能需要从 IdP 进行调用。

### <a name="deploy-applications-and-user-identities"></a>部署应用程序和用户标识

所有 Azure AD B2C 项目都从一个或多个客户端应用程序开始，这些应用程序可能具有不同的业务目标。

1. [创建或配置客户端应用程序](../../active-directory-b2c/app-registrations-training-guide.md)。 请参阅这些[代码示例](../../active-directory-b2c/integrate-with-app-code-samples.md)了解如何实现。

2. 接下来，根据内置或自定义用户流设置用户旅程。 [了解用户流与自定义策略的使用时机](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies)。

3. 根据业务需求设置 IdP。 [了解如何将 Azure Active Directory B2C 添加为 IdP](../../active-directory-b2c/add-identity-provider.md)。

4. 迁移用户。 [了解用户迁移方法](../../active-directory-b2c/user-migration.md)。 有关高级方案，请参阅 [Azure AD B2C IEF 示例用户旅程](https://github.com/azure-ad-b2c/samples)。  

部署应用程序时，请考虑以下示例清单：

- 检查 CIAM 部署范围内的应用程序数量。

- 检查正在使用的应用程序类型。 例如，传统的 Web 应用程序、API、单页应用 (SPA) 或本机移动应用程序。

- 检查所采用的身份验证类型。 例如，基于窗体、与 SAML 联合或与 OIDC 联合。
  - 如果与 OIDC 联合，请检查响应类型 - 代码或 id_token。

- 检查所有前端和后端应用程序是托管在本地、云还是混合云中。

- 检查所使用的平台/语言，例如 [ASP.NET](../../active-directory-b2c/quickstart-web-app-dotnet.md)、Java 和 Node.js。

- 检查当前用户属性的存储位置。 它可以是轻型目录访问协议 (LDAP) 或数据库。

部署用户标识时，请考虑以下示例清单：

- 检查访问应用程序的用户数量。

- 检查所需的 IdP 类型。 例如，Facebook、本地帐户和 [Active Directory 联合身份验证服务 (AD FS)](/windows-server/identity/active-directory-federation-services)。

- 概述应用程序、[Azure AD B2C](../../active-directory-b2c/claimsschema.md) 和 IdP（如果适用）所需的声明架构。

- 概述在[登录/注册流](../../active-directory-b2c/add-sign-up-and-sign-in-policy.md?pivots=b2c-user-flow)期间需要捕获的信息。

### <a name="client-application-onboarding-and-deliverables"></a>客户端应用程序载入和可交付结果

载入应用程序时，请考虑以下示例清单：

|  任务 | 说明 |
|:--------------------|:----------------------|
| 定义应用程序的目标组 | 检查此应用程序是最终客户应用程序、企业客户应用程序还是数字服务。 检查是否需要员工登录。 |
| 确定应用程序背后的业务价值 | 了解应用程序背后的完整业务案例，找到最适合的 Azure AD B2C 解决方案以及与更多客户端应用程序的集成。|
| 检查你拥有的标识组 | 具有不同类型要求的不同类型组中的群集标识，例如面向最终客户和企业客户的 **企业到客户** (B2C)，面向合作伙伴和供应商的 **企业到企业** (B2B)，面向员工和外部员工的 **企业到员工** (B2E)，面向 IoT 设备登录和服务帐户的 **企业到计算机** (B2M)。|
| 检查业务需求和流程所需的 IdP | Azure AD B2C [支持多种类型的 IdP](../../active-directory-b2c/add-identity-provider.md#select-an-identity-provider)，并且应根据用例选择正确的 IdP。 例如，对于客户到客户移动应用程序，需要快速简便的用户登录。 在另一个用例中，对于具有数字服务的企业到客户应用程序，需要满足其他合规性要求。 用户可能需要使用企业标识登录，例如电子邮件登录。 |
| 检查法规约束 | 检查是否出于任何原因具有远程配置文件或特定隐私策略。  |
| 设计登录和注册流 | 确定是需要电子邮件验证还是注册内的电子邮件验证。 是否需要首次结帐流程（例如 Shop 系统）或 [Azure AD 多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)。 观看[此视频](https://www.youtube.com/watch?v=c8rN1ZaR7wk&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=4)。 |
| 检查已使用或将实现的应用程序和身份验证协议的类型 | 有关客户端应用程序（例如 Web 应用程序、SPA 或本机应用程序）实现的信息交换。 客户端应用程序和 Azure AD B2C 的身份验证协议可以是 OAuth、OIDC 和 SAML。 观看[此视频](https://www.youtube.com/watch?v=r2TIVBCm7v4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=9)|
| 规划用户迁移 | 讨论[使用 Azure AD B2C 进行用户迁移](../../active-directory-b2c/user-migration.md)的可能性。 有几种可能的方案，例如实时 (JIT) 迁移和批量导入/导出。 观看[此视频](https://www.youtube.com/watch?v=lCWR6PGUgz0&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=2)。 你还可以考虑使用 [Microsoft Graph API](https://www.youtube.com/watch?v=9BRXBtkBzL4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=3) 进行用户迁移。|

交付时考虑以下示例清单。

| 功能 | 说明 |
|:-----|:-------|
|协议信息| 收集两个变体的基本路径、策略和元数据 URL。 根据客户端应用程序，指定示例登录名、客户端应用程序 ID、机密和重定向等属性。|
| 应用程序示例 | 请参阅提供的[示例代码](../../active-directory-b2c/integrate-with-app-code-samples.md)。 |
|渗透测试 | 在测试之前，将渗透测试告知运营团队，然后测试所有用户流，包括 OAuth 实现。 详细了解[渗透测试](../../security/fundamentals/pen-testing.md)，以及 [Microsoft 云渗透测试统一参与规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement)。
| 单元测试  | [使用资源所有者密码凭据 (ROPC) 流](../develop/v2-oauth-ropc.md)执行单元测试并生成令牌。 如果达到 Azure AD B2C 令牌限制，请[联系支持团队](../../active-directory-b2c/support-options.md)。 重复使用令牌，以减少对对基础结构的调查工作。 [设置 ROPC 流](../../active-directory-b2c/add-ropc-policy.md?pivots=b2c-user-flow&tabs=app-reg-ga)。|
| 负载测试 | 预期达到 Azure AD B2C [服务限制](../../active-directory-b2c/service-limits.md)。 评估服务每月的预期身份验证次数。 评估每月预期的平均用户登录次数。 评估预期的高负载流量持续时间和业务原因，例如节假日、迁移和事件。 评估预期的峰值注册速率，例如每秒请求数。 评估 MFA 的预期峰值流量速率，例如每秒请求数。 评估预期的流量地理分布及其峰值速率。

### <a name="security"></a>安全

考虑使用以下示例清单，根据业务需求来增强应用程序的安全性：

- 检查是否需要强身份验证方法，例如 [MFA](../authentication/concept-mfa-howitworks.md)。 对于触发高价值交易或其他风险事件的用户，建议使用 MFA。 例如，对于银行和财务应用程序、网上商店，需要使用首次结帐流程。

- 检查是否需要 MFA，[检查可用于执行 MFA 的方法](../authentication/concept-authentication-methods.md)，例如短信/电话、电子邮件和第三方服务。

- 检查应用程序当前是否正在使用任何反机器人机制。

- 评估尝试创建欺诈帐户和登录的风险。使用 [Microsoft Dynamics 365 欺诈保护评估](../../active-directory-b2c/partner-dynamics-365-fraud-protection.md)来阻止或质询尝试新建虚假帐户或损害现有帐户的可疑行为。  

- 检查需要在登录或注册应用程序帐户的过程中应用的任何特殊条件状态。

>[!NOTE]
>可以使用[条件访问规则](../conditional-access/overview.md)根据业务目标来调整用户体验和安全性之间的差异。

有关详细信息，请参阅 [Azure AD B2C 中的标识保护和条件访问](../../active-directory-b2c/conditional-access-identity-protection-overview.md)。

### <a name="compliance"></a>合规性

为了满足某些法规要求，可以考虑使用 vNet、IP 限制、Web 应用程序防火墙 (WAF) 和类似服务来增强后端系统的安全性。

若要满足基本的合规性要求，请考虑：

- 特定的法规合规性要求，例如，需要支持的 PCI-DSS。

- 检查是否需要将数据存储到单独的数据库存储中。 如果需要，请检查此信息是否永远不能写入目录。

### <a name="user-experience"></a>用户体验

考虑使用示例清单来定义用户体验 (UX) 要求：

- 确定[扩展 CIAM 功能和生成无缝最终用户体验](../../active-directory-b2c/partner-gallery.md)所需的集成。

- 提供屏幕截图和用户情景，以展示现有应用程序的最终用户体验。 例如，提供登录、注册、组合注册登录 (SUSI)、个人资料编辑和密码重置的屏幕截图。

- 在当前的 CIAM 解决方案中查找使用 queryString 参数传递的现有提示。

- 如果你希望对 UX 进行高度自定义，例如像素到像素，可能需要前端开发人员来帮助你。

## <a name="monitor-an-azure-ad-b2c-solution"></a>监视 Azure AD B2C 解决方案

此阶段包括以下功能：

| 功能 | 说明 |
|:---------|:----------|
|  监视  |[使用 Azure Monitor 监视 Azure AD B2C](../../active-directory-b2c/azure-monitor.md)。 观看[此视频](https://www.youtube.com/watch?v=Mu9GQy-CbXI&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=1)|
| 审核和日志记录 | [访问和查看审核日志](../../active-directory-b2c/view-audit-logs.md)

## <a name="more-information"></a>详细信息

若要加快 Azure AD B2C 部署并大规模监视服务，请参阅以下文章：

- [使用 Microsoft Graph 管理 Azure AD B2C](../../active-directory-b2c/microsoft-graph-get-started.md)

- [使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](../../active-directory-b2c/microsoft-graph-operations.md)

- [使用 Azure Pipelines 部署自定义策略](../../active-directory-b2c/deploy-custom-policies-devops.md)

- [使用 Azure PowerShell 管理 Azure AD B2C 自定义策略](../../active-directory-b2c/manage-custom-policies-powershell.md)

- [使用 Azure Monitor 监视 Azure AD B2C](../../active-directory-b2c/azure-monitor.md)

## <a name="next-steps"></a>后续步骤

- [Azure AD B2C 最佳做法](../../active-directory-b2c/best-practices.md)

- [Azure AD B2C 服务限制](../../active-directory-b2c/service-limits.md)