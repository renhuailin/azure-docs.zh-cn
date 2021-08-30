---
title: Azure AD 应用程序注册配置的最佳做法 - Microsoft 标识平台
description: 了解有关 Azure AD 应用程序注册配置的一组最佳做法和常规指南。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/8/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: saumadan, marsma
ms.openlocfilehash: 55633a1a3a6f4377abbfc413d866af031f57a31c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742910"
---
# <a name="azure-ad-application-registration-security-best-practices"></a>Azure AD 应用程序注册的安全最佳做法

Azure Active Directory (Azure AD) 应用程序注册是业务应用程序的关键部分。 应用程序的任何配置错误或清理失误都可能导致故障时间或泄露。

理解应用程序注册比业务应用程序的影响要更广泛（因为其外围应用），这一点很重要。 根据添加到应用程序的权限，已泄露的应用可能会影响到整个组织。
由于要使用户登录，应用程序注册是必需的，因此任何故障时间都可能会对业务或者业务所依赖的某些关键服务产生影响。 因此，分配时间和资源来确保应用程序注册始终处于正常运行状态很重要。 建议你定期对应用程序进行安全和运行状况评估，这与代码的安全威胁模型评估非常类似。 若要更广泛地了解组织的安全性，请查看[安全开发生命周期](https://www.microsoft.com/securityengineering/sdl) (SDL)。

本文介绍了以下应用程序注册属性的安全最佳做法。

- 重定向 URI
- 访问令牌的隐式授权流
- 凭据
- AppId URI
- 应用程序所有权
- 清单

## <a name="redirect-uri-configuration"></a>重定向 URI 配置

将应用程序重定向 URI 保持在最新状态很重要。 一个重定向 URI 的所有权失误就可能导致应用程序泄露。 请确保定期更新并监视所有 DNS 记录是否有更改。 除了维护所有 URI 的所有权外，请不要使用通配符回复 URI 或不安全的 URI 方案，如 http 或 URN。

![重定向 URI](media/active-directory-application-registration-best-practices/redirect-uri.png)

### <a name="redirect-uri-summary"></a>重定向 URI 摘要

| 应做事项                                    | 禁止事项          |
| ------------------------------------- | -------------- |
| 维护所有 URI 的所有权        | 使用通配符  |
| 确保 DNS 为最新版本                   | 使用 URN 方案 |
| 使列表保持较小                   |   -----        |
| 剪裁任何不必要的 URI             |   -----        |
| 将 URL 从 Http 更新为 Https 方案 |   -----        |

## <a name="implicit-flow-token-configuration"></a>隐式流令牌配置

需要隐式流的方案现可使用身份验证代码流，来降低与隐式授权流误用相关的泄露风险 。 如果已将应用程序注册配置为使用隐式流获取访问令牌，但不常使用它，则建议关闭该设置以防止误用。

![用于隐式流的访问令牌](media/active-directory-application-registration-best-practices/implict-grant-flow.png)

### <a name="implicit-grant-flow-summary"></a>隐式授权流摘要

| 应做事项                                                                    | 禁止事项                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| 了解是否[需要隐式流](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) | 使用隐式流，除非[明确要求](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| 为（有效）隐式流方案提供单独的应用注册 |   -----                                                |
| 关闭未使用的隐式流 | -----              |

## <a name="credential-configuration"></a>凭据配置

当应用程序用作机密客户端时，凭据是应用程序注册的重要部分。 如果你的应用注册仅用作公用客户端应用（允许用户使用公共终结点登录），请确保你的应用程序对象没有任何凭据。 查看应用程序中所使用凭据的使用频率和过期时间。 应用程序上未使用的凭据可能会导致安全漏洞。
尽管使用密码机密作为凭据很方便，但我们强烈建议你使用 x509 证书作为获取应用程序的令牌的唯一凭据类型。 监视生产管道，确保任何类型的凭据都不会提交到代码存储库中。 如果使用 Azure，我们强烈建议使用托管标识，以便自动管理应用程序凭据。 有关更多详细信息，请参阅[托管标识文档](../managed-identities-azure-resources/overview.md)。 [凭据扫描器](../../security/develop/security-code-analysis-overview.md#credential-scanner)是一种可用来检测源代码和生成输出中的凭据（和其他敏感内容）的静态分析工具。

![Azure 门户上的证书和机密](media/active-directory-application-registration-best-practices/credentials.png)

| 应做事项                                                                     | 禁止事项                             |
| ---------------------------------------------------------------------- | --------------------------------- |
| 使用[证书凭据](./active-directory-certificate-credentials.md)              | 使用密码凭据          |
| 将密钥保管库与[托管标识](../managed-identities-azure-resources/overview.md)结合使用 | 跨应用共享凭据     |
| 经常滚动更新                                                    | 在一个应用上拥有多个凭据  |
|     -----                                                              | 将过时凭据保持在可用状态 |
|     -----                                                              | 在代码中提交凭据        |

## <a name="appid-uri-configuration"></a>AppId URI 配置

某些应用程序可以（通过 WebAPI）公开资源，因此需要定义一个可唯一标识租户中资源的 AppId URI。 建议使用以下 URI 方案之一：api 或 https，并将 AppId URI 设置为以下格式，以避免组织中出现 URI 冲突。

**有效的 api 方案：**

- api://{appId}
- api://{tenantId}/{appId}
- api://{tenantId}/{string}
- https://{verifiedCustomerDomain}/{string}
- https://{string}.{verifiedCustomerDomain}
- https://{string}.{verifiedCustomerDomain}/{string}

![应用程序 ID URI](media/active-directory-application-registration-best-practices/app-id-uri.png)

### <a name="appid-uri-summary"></a>AppId URI 摘要

| 应做事项                                           | 禁止事项                  |
| -------------------------------------------- | ---------------------- |
| 使用有效的 URI 格式避免冲突。 | 使用通配符 AppId URI |
| 使用业务线 (LoB) 应用中已验证的域 | 格式不正确的 URI    |
| 盘存你的 AppId URI                    |      -----             |

## <a name="app-ownership-configuration"></a>应用所有权配置

确保使组织中最小一组人员拥有应用所有权。 建议每几个月运行一次所有者列表，来确保所有者仍然是组织及其特许帐户的一部分，从而获取应用程序注册的所有权。 有关更多详细信息，请参阅 [Azure AD 访问评审](../governance/access-reviews-overview.md)。

![用户预配服务 - 所有者](media/active-directory-application-registration-best-practices/app-ownership.png)

### <a name="app-ownership-summary"></a>应用所有权摘要

| 应做事项                  | 禁止事项 |
| ------------------- | ----- |
| 使其保持较小       | ----- |
| 监视所有者列表 | ----- |

## <a name="checklist"></a>清单

应用开发人员可使用 Azure 门户中提供的清单，来确保其应用注册满足高质量标准并提供安全集成的指导。 集成助手突出显示了在与 Microsoft 标识平台集成时有助于避免常见疏忽的最佳做法和建议。

![Azure 门户上的集成助手清单](media/active-directory-application-registration-best-practices/checklist.png)

### <a name="checklist-summary"></a>清单摘要

| 应做事项                                                 | 禁止事项 |
| -------------------------------------------------- | ----- |
| 使用清单获取基于方案的建议 | ----- |
| 应用注册边栏选项卡的深层链接             | ----- |


## <a name="next-steps"></a>后续步骤
有关身份验证代码流的详细信息，请参阅 [OAuth 2.0 授权代码流](./v2-oauth2-auth-code-flow.md)。