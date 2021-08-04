---
title: 有关 Azure AD 条件访问身份验证上下文的开发人员指南
description: 有关 Azure AD 条件访问身份验证上下文的开发人员指南和方案
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: CelesteDG
ms.reviewer: kkrishna
ms.workload: identity
ms.custom: aaddev
ms.openlocfilehash: b388c61c048cdb24f53ba55dd55024d676395a5e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471340"
---
# <a name="developers-guide-to-conditional-access-authentication-context"></a>有关条件访问身份验证上下文的开发人员指南

[条件访问](../conditional-access/overview.md)是零信任控制平面，可让你有针对性地使用策略来控制对所有应用（旧应用或新应用、专用应用或公共应用、本地应用或多云应用）的访问。 使用[条件访问身份验证上下文](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)可以在这些应用中应用不同的策略。 

条件访问身份验证上下文（身份验证上下文）可让你将精细策略应用于敏感数据和操作，而不仅仅是在应用级别应用此类策略。 可以具体化零信任策略以实现最低特权的访问，同时最大程度地减少用户之间的冲突，并使用户保持较高的工作效率，使资源保持较高的安全性。 现在，由你的公司开发的、使用 [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html) 进行身份验证的应用程序可以通过此功能来保护敏感资源（例如高价值的交易）或查看员工个人数据。

使用 Azure AD 条件访问引擎的新身份验证上下文功能可以触发从应用程序和服务内部执行升级身份验证的要求。 开发人员现在可以选择性地要求执行增强的身份验证，类似于要求其最终用户从其应用程序内部执行 MFA。 此功能可帮助开发人员为其应用程序的大多数部件构建更流畅的用户体验，同时可以访问更安全的操作，并且数据将受到更强大身份验证的控制。

## <a name="problem-statement"></a>问题陈述

在过于频繁地提示用户采取额外的身份验证措施，与在包含敏感数据和操作的部件的应用程序和服务中实现足够高的安全性与策略遵从性之间，IT 管理员和调控员往往很难做出取舍。 可以做出的选择是采用一个强策略，但这会影响用户在访问大部分数据和操作时的工作效率；或者采用一个强度不足以保护敏感资源的策略。 

如果应用能够混合使用这两种策略，即，能够以相对较低的安全性运行，但又不那么频繁地提示用户和操作进行身份验证，同时在用户访问更敏感的信息部分时按条件升级安全要求，那么效果又如何呢？ 

## <a name="common-scenarios"></a>常见方案

例如，尽管用户可以使用多重身份验证登录到 SharePoint，但在 SharePoint 中访问包含敏感文档的网站集可能需要使用合规的设备，并且只能从受信任的 IP 范围访问。 

## <a name="steps"></a>步骤

下面是使用条件访问身份验证上下文所要满足的先决条件和执行的步骤。 

### <a name="prerequisites"></a>先决条件

首先，应使用用于身份验证和授权的 [OpenID Connect](v2-protocols-oidc.md)/ [OAuth 2.0](v2-oauth2-auth-code-flow.md) 协议将应用与 Microsoft 标识平台集成。 建议使用 [Microsoft 标识平台身份验证库](reference-v2-libraries.md)在 Azure Active Directory 中集成和保护应用程序。 [Microsoft 标识平台文档](index.yml)是一个很好的学习资源，其中介绍了如何将应用与 Microsoft 标识平台集成。 条件访问身份验证上下文功能支持构建在行业标准 [OpenID Connect](v2-protocols-oidc.md) 协议提供的协议扩展基础之上。 开发人员对[声明请求](claims-challenge.md)参数使用一个[条件访问身份验证上下文参考](/graph/api/resources/authenticationcontextclassreference)值即可让应用触发和满足策略。

其次，[条件访问](../conditional-access/overview.md)需要获得 Azure AD Premium P1 许可。 在 [Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到有关许可的详细信息。

第三，此功能目前仅适用于可将用户登录的应用程序。 不支持自身进行身份验证的应用程序。 请参考[身份验证流和应用程序方案指南](authentication-flows-app-scenarios.md)了解 Microsoft 标识平台中支持的身份验证应用类型和流。

### <a name="integration-steps"></a>集成步骤

使用支持的身份验证协议集成应用程序并将其注册到具有可用条件访问功能的 Azure AD 租户后，可以开始将此功能集成到可将用户登录的应用程序中。

首先，声明身份验证上下文并使其在租户中可用。 有关详细信息，请参阅[配置身份验证上下文](../conditional-access/concept-conditional-access-cloud-apps.md#configure-authentication-contexts)

值 C1-C25 可用作租户中的身份验证上下文 ID 。 身份验证上下文的示例：

- C1 – 需要强身份验证
- C2 – 需要合规的设备
- C3 – 需要受信任位置

创建或修改条件访问策略，以使用条件访问身份验证上下文。 示例策略：

- 登录到此 Web 应用程序的所有用户应已成功完成身份验证上下文 ID C1 的 2FA。
- 登录到此 Web 应用程序的所有用户应已成功完成 2FA，并从身份验证上下文 ID C3 的特定 IP 地址范围访问此 Web 应用。

> [!NOTE]
> 条件访问身份验证上下文值是与应用程序分开声明和维护的。 不建议应用程序硬性依赖于身份验证上下文 ID。 条件访问策略通常由 IT 管理员制定，因为他们更了解可应用这些策略的资源。 例如，对于 Azure AD 租户，IT 管理员知道该租户中有多少用户有条件使用 2FA 进行 MFA，从而确保将需要 2FA 的条件访问策略的范围限定为这些有条件的用户。 同样，如果在多个租户中使用该应用程序，则使用的身份验证上下文 ID 可以不同，并且在某些情况下可以完全不可用。

其次，建议打算使用条件访问身份验证上下文的应用程序开发人员首先为应用程序管理员或 IT 管理员提供一种将潜在敏感的操作映射到身份验证上下文 ID 的方式。 步骤大致是：

1. 在代码中识别可用于映射到身份验证上下文 ID 的操作。
1. 在应用的管理门户（或等效功能）中构建一个屏幕，供 IT 管理员用来将敏感操作映射到可用的身份验证上下文 ID。
1. 有关此方法的示例，请参阅代码示例[使用条件访问身份验证上下文执行升级身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。

这些步骤是需要在基础代码中携带的更改。 概括而言，这些步骤包括

- 查询 MS Graph 以[列出所有可用的身份验证上下文](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)。
- 允许 IT 管理员选择敏感/高特权操作，并使用 CA 策略根据可用的身份验证上下文分配这些操作。 
- 将此映射信息保存在数据库/本地存储中。

:::image type="content" source="media/developer-guide-conditional-access-authentication-context/configure-conditional-access-authentication-context.png" alt-text="用于创建身份验证上下文的设置流":::

第三，应用程序（在本示例中，我们假设它是一个 Web API）需要根据保存的映射评估调用，并相应地对其客户端应用提出声明质询。 若要准备此操作，需要执行以下步骤：

1. 在敏感的且受身份验证上下文保护的操作中，根据前面保存的身份验证上下文 ID 映射评估 acrs 声明中的值，并提出以下代码片段中提供的声明质询。 

1. 下图显示了用户、客户端应用与 Web API 之间的交互。

   :::image type="content" source="media/developer-guide-conditional-access-authentication-context/authentication-context-application-flow.png" alt-text="显示用户、Web 应用、API 与 Azure AD 之间的交互的示意图":::

   以下代码片段摘自代码示例[使用条件访问身份验证上下文执行升级身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。 API 中的第一个方法 `CheckForRequiredAuthContext()` 

      - 检查所调用的应用程序操作是否需要升级身份验证。 为此，它会检查其数据库中是否为此方法保存了映射
      - 如果此操作确实需要提升的身份验证上下文，则它会检查 acrs 声明中是否存在现有的匹配身份验证上下文 ID。
      - 如果找不到匹配的身份验证上下文 ID，则它会提出[声明质询](claims-challenge.md#claims-challenge-header-format)。

      ```
      public void CheckForRequiredAuthContext(string method)
      {
          string authType = _commonDBContext.AuthContext.FirstOrDefault(x => x.Operation == method 
                      && x.TenantId == _configuration["AzureAD:TenantId"])?.AuthContextId;

          if (!string.IsNullOrEmpty(authType))
          {
              HttpContext context = this.HttpContext;
              string authenticationContextClassReferencesClaim = "acrs";

              if (context == null || context.User == null || context.User.Claims == null 
                  || !context.User.Claims.Any())
              {
                  throw new ArgumentNullException("No Usercontext is available to pick claims from");
              }

              Claim acrsClaim = context.User.FindAll(authenticationContextClassReferencesClaim).FirstOrDefault(x 
                  => x.Value == authType);

              if (acrsClaim == null || acrsClaim.Value != authType)
              {
                  if (IsClientCapableofClaimsChallenge(context))
                  {
                      string clientId = _configuration.GetSection("AzureAd").GetSection("ClientId").Value;
                      var base64str = Convert.ToBase64String(Encoding.UTF8.GetBytes("{\"access_token\":{\"acrs\":{\"essential\":true,\"value\":\"" + authType + "\"}}}"));

                      context.Response.Headers.Append("WWW-Authenticate", $"Bearer realm=\"\", authorization_uri=\"https://login.microsoftonline.com/common/oauth2/authorize\", client_id=\"" + clientId + "\", error=\"insufficient_claims\", claims=\"" + base64str + "\", cc_type=\"authcontext\"");
                      context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
                      string message = string.Format(CultureInfo.InvariantCulture, "The presented access tokens had insufficient claims. Please request for claims requested in the WWW-Authentication header and try again.");
                      context.Response.WriteAsync(message);
                      context.Response.CompleteAsync();
                      throw new UnauthorizedAccessException(message);
                  }
                  else
                  {
                      throw new UnauthorizedAccessException("The caller does not meet the authentication  bar to carry our this operation. The service cannot allow this operation");
                  }
              }
          }
      }
      ```

   > [!NOTE]
   > [Microsoft 标识平台中的声明质询](claims-challenge.md)一文介绍了声明质询的格式。 

1. 在客户端应用程序中，截获声明质询并将用户重定向回到 Azure AD 以进一步评估策略。 以下代码片段摘自代码示例[使用条件访问身份验证上下文执行升级身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。

   ```
   internal static string ExtractHeaderValues(WebApiMsalUiRequiredException response)
   {
       if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized && response.Headers.WwwAuthenticate.Any())
       {
           AuthenticationHeaderValue bearer = response.Headers.WwwAuthenticate.First(v => v.Scheme == "Bearer");
           IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
           var errorValue = GetParameterValue(parameters, "error");

           try
           {
               // read the header and checks if it conatins error with insufficient_claims value.
               if (null != errorValue && "insufficient_claims" == errorValue)
               {
                   var claimChallengeParameter = GetParameterValue(parameters, "claims");
                   if (null != claimChallengeParameter)
                   {
                       var claimChallenge = ConvertBase64String(claimChallengeParameter);

                       return claimChallenge;
                   }
               }
           }
           catch (Exception ex)
           {
               throw ex;
           }
       }
       return null;
   }
   ```

   处理 Web API 调用中的异常，如果提供了声明质询，则将用户重定向回到 Azure AD 以做进一步处理。

   ```
   try
   {
       // Call the API 
       await _todoListService.AddAsync(todo);
   }
   catch (WebApiMsalUiRequiredException hex)
   {
       // Challenges the user if exception is thrown from Web API.
       try
       {
           var claimChallenge =ExtractHeaderValues(hex);
           _consentHandler.ChallengeUser(new string[] { "user.read" }, claimChallenge);

           return new EmptyResult();
       }
       catch (Exception ex)
       {
           _consentHandler.HandleException(ex);
       }

       Console.WriteLine(hex.Message);
   }
   return RedirectToAction("Index");
   ```
   
1. （可选）声明客户端功能。 客户端功能可帮助资源提供程序 (RP)（例如上述 Web API）检测调用方客户端应用程序是否了解声明质询，然后可以相应地自定义其响应。 在并非所有 API 客户端都能处理声明质询，并且某些旧式客户端仍然需要不同响应的情况下，此功能可能很有用。 有关详细信息，请参阅[客户端功能](claims-challenge.md#client-capabilities)部分。

## <a name="caveats-and-recommendations"></a>注意事项和建议

不要在应用中对身份验证上下文值进行硬编码。 应用应该[使用 MS Graph 调用](/graph/api/resources/authenticationcontextclassreference)来读取和应用身份验证上下文。 这种做法对于[多租户应用程序](howto-convert-app-to-be-multi-tenant.md)而言至关重要。 身份验证上下文值根据 Azure AD 租户的不同而异，在 Azure AD 免费版中不可用。 有关应用应该如何在其代码中查询、设置和使用身份验证上下文的详细信息，请参阅代码示例[使用条件访问身份验证上下文执行升级身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。 

如果应用本身将成为条件访问策略的目标，请不要在该应用中使用身份验证上下文。 当应用程序的某些部件要求用户满足较高的身份验证限制时，此功能可发挥最大的作用。

## <a name="next-steps"></a>后续步骤

- 博客文章 [Granular Conditional Access for sensitive data and actions](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)（针对敏感的数据和操作实施精细条件访问）
- [Microsoft 标识平台中的零信任](/security/zero-trust/identity-developer)
- [使用 Microsoft 标识平台构建零信任就绪应用](/security/zero-trust/identity-developer)
- [使用条件访问身份验证上下文对 Web API 中的高特权操作执行升级身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)
- [条件访问身份验证上下文](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)
- [authenticationContextClassReference 资源类型 - MS Graph](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)
- [Microsoft 标识平台中的声明质询、声明请求和客户端功能](claims-challenge.md)
- [在 Microsoft 信息保护和 SharePoint 中使用身份验证上下文](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
- [如何在应用程序中使用启用了连续访问评估的 API](app-resilience-continuous-access-evaluation.md)
