---
title: Azure AD 条件访问身份验证上下文的开发人员指南
description: Azure AD 条件访问身份验证上下文的开发人员指南和方案
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
ms.openlocfilehash: 0a310df6793d2caecda095f0c89244ffee5e46eb
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728023"
---
# <a name="developers-guide-to-conditional-access-authentication-context"></a>有关条件访问身份验证上下文的开发人员指南

[条件访问](../conditional-access/overview.md)是零信任控制平面，可让你有针对性地使用策略来控制对所有应用（旧应用或新应用、专用应用或公共应用、本地应用或多云应用）的访问。 使用[条件访问身份验证上下文](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)可以在这些应用中应用不同的策略。 

条件访问身份验证上下文（身份验证上下文）允许将具体策略应用于敏感数据和操作，而不仅仅是在应用级别。 可以优化零信任策略来实现最低权限的访问，同时最大限度地减少用户摩擦，让用户更有生产力，让资源更加安全。 目前，使用 [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html) 的应用程序可通过条件访问进行身份验证，这些应用程序由公司开发以保护敏感资源，例如高价值事务或用于查看员工个人数据。

使用 Azure AD 条件访问引擎的新身份验证上下文功能从应用程序和服务中触发对逐步身份验证的需求。 现在，开发人员可以有选择地要求实现更强身份验证，例如来自最终用户在其应用程序中的 MFA。 此功能有助于开发人员为其应用程序的大多部分构建更流畅的用户体验，但对更安全的操作和数据的访问仍滞后于更强身份验证控制。

## <a name="problem-statement"></a>问题陈述

IT 管理员和监管人员通常对以下内容比较纠结，即如何平衡过于频繁地使用额外的身份验证因素提示用户，同时确保部分包含敏感数据和操作的应用程序和服务遵守安全性和策略。 可以选择在用户访问大多数数据和操作时会影响用户工作效率的强策略，也可以选择对敏感资源不够强的策略。 

那么应用是否能够实现这二者的结合，即在大多数用户和操作中，它们能够以相对较低的安全性和较少的提示次数运行，但当用户访问更敏感的部分时，又有条件地提高安全要求呢？ 

## <a name="common-scenarios"></a>常见方案

例如，虽然用户可使用多重身份验证登录到 SharePoint，但访问包含敏感文档的 SharePoint 中的网站集可能需要兼容设备，并且只能通过受信任的 IP 范围进行访问。 

## <a name="steps"></a>步骤

以下是要使用条件访问身份验证上下文的先决条件和步骤。 

### <a name="prerequisites"></a>先决条件

首先，应用应与 Microsoft 标识平台集成，该平台使用 [OpenID Connect](v2-protocols-oidc.md)/ [OAuth 2.0](v2-oauth2-auth-code-flow.md) 协议进行身份验证和授权。 建议使用 [Microsoft 标识平台身份验证库](reference-v2-libraries.md)将应用程序与 Azure Active Directory 集成并确保应用安全。 [Microsoft 标识平台文档](index.yml)是学习如何将应用与 Microsoft 标识平台集成的理想起点。 条件访问身份验证上下文功能支持建立在行业标准 [OpenID Connect](v2-protocols-oidc.md) 协议提供的协议扩展之上。 开发人员将[条件访问身份验证上下文引用](/graph/api/resources/authenticationcontextclassreference)值与[声明请求](claims-challenge.md)参数一起用于为应用提供触发和满足策略的方法。

第二，[条件访问](../conditional-access/overview.md)需要 Azure AD Premium P1 许可。 有关许可的更多信息，请参阅 [Azure AD 定价页](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

第三，目前它仅适用于可让用户登录的应用程序。 不支持以自身身份进行身份验证的应用程序。 使用[身份验证流和应用程序方案指南](authentication-flows-app-scenarios.md)了解 Microsoft 标识平台中支持的身份验证应用类型和流。

### <a name="integration-steps"></a>集成步骤

使用受支持的身份验证协议集成应用程序，并在具有可用条件访问功能的 Azure AD 租户中注册后，即可开启在让用户登录的应用程序中集成此功能的过程。

> [!NOTE]
> [使用应用程序中的条件访问身份验证上下文进行逐步身份验证](https://www.youtube.com/watch?v=_iO7CfoktTY)中的录制会议中也提供了此功能的详细演示。

首先，声明身份验证上下文并使其在你的租户中可用。 有关详细信息，请参阅[配置身份验证上下文](../conditional-access/concept-conditional-access-cloud-apps.md#configure-authentication-contexts)。

值 C1-C25 可用作租户中的身份验证上下文 ID 。 身份验证上下文的示例可能为：

- C1 – 需要强身份验证
- C2 – 需要合规的设备
- C3 – 需要受信任位置

创建或修改条件访问策略，以使用条件访问身份验证上下文。 示例策略可以是：

- 登录此 Web 应用程序的所有用户应该都已成功完成身份验证上下文 ID C1 的 2FA。
- 所有登录此 Web 应用程序的用户应该都已成功完成 2FA，并且还可以从身份验证上下文 ID C3 的某个 IP 地址范围访问 Web 应用。

> [!NOTE]
> 条件访问身份验证上下文值与应用程序分开进行声明和维护。 不建议应用程序过分依赖身份验证上下文 ID。 条件访问策略通常由 IT 管理员制定，因为他们对可用于应用策略的资源有更深入的了解。 例如，对于 Azure AD 租户，IT 管理员知道有多少租户的用户能够使用 2FA 进行 MFA，因此他们可以确保需要 2FA 的条件访问策略的范围是这些已经配备的用户。 同样，如果在多个租户中使用该应用程序，则使用中的身份验证上下文 ID 可能会不同，并且在某些情况下根本不可用。

第二：对于计划使用条件访问身份验证上下文的应用程序，建议其开发人员首先为应用程序管理员或 IT 管理员提供一种方法，将潜在敏感操作映射到身份验证上下文 ID。 步骤大致如下：

1. 标识代码中的操作，代码可用于根据身份验证上下文 ID 进行映射。
1. 在应用的管理门户（或等效功能）中构建一个屏幕，IT 管理员可以使用该屏幕将敏感操作映射到可用的身份验证上下文 ID。
1. 有关操作方法的示例，请参阅代码示例[使用条件访问身份验证上下文进行逐步身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。

这些步骤是你需要在代码库中执行的改变。 这些步骤大致包括：

- 查询 MS Graph，[列出所有可用的身份验证上下文](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)。
- 允许 IT 管理员选择敏感/高权限操作，并使用 CA 策略针对可用的身份验证上下文分配这些操作。 
- 将此映射信息保存在数据库/本地存储中。

:::image type="content" source="media/developer-guide-conditional-access-authentication-context/configure-conditional-access-authentication-context.png" alt-text="创建身份验证上下文的设置流":::

第三：你的应用程序，在此示例中，我们假设它是一个 Web API，然后需要根据保存的映射评估调用，并相应地针对其客户端应用提出声明质询。 若要准备此操作，需要执行以下步骤：

1. 在受身份验证上下文操作保护的敏感环境中，根据前面保存的身份验证上下文 ID 映射评估 acrs 声明中的值，并引发[声明质询](claims-challenge.md)，如以下代码片段所示。 

1. 下图显示了用户、客户端应用和 Web API 之间的交互。

   :::image type="content" source="media/developer-guide-conditional-access-authentication-context/authentication-context-application-flow.png" alt-text="显示用户、Web 应用、API 和 Azure AD 之间交互的关系图":::

   以下代码片段来自代码示例[使用条件访问身份验证上下文进行逐步身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。 API 中的第一个方法 `CheckForRequiredAuthContext()` 

      - 检查所调用的应用程序操作是否需要进行逐步身份验证。 为此，它会检查其数据库中是否保存了此方法的映射
      - 如果此操作确实需要提升的身份验证上下文，它会检查 acrs 声明中现有匹配的身份验证上下文 ID。
      - 如果找不到匹配的身份验证上下文 ID，它会引发[声明质询](claims-challenge.md#claims-challenge-header-format)。

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
   > 有关声明质询的格式，请参阅 [Microsoft 标识平台中的声明质询](claims-challenge.md)一文。 

1. 在客户端应用程序中，截获声明质询，将用户重定向回 Azure AD 进行进一步的策略评估。 以下代码片段来自代码示例[使用条件访问身份验证上下文进行逐步身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)。

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

   处理对 Web API 的调用中的异常，如果显示声明质询，则用户将重定向回 Azure AD 进行进一步处理。

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
   
1. （可选）声明客户端功能。 客户端功能有助于 Web API 等资源提供程序 (RP) 检测调用方客户端应用程序是否理解声明质询，然后可以相应地自定义其响应。 如果并非所有 API 客户端都能处理声明质询，并且某些较旧的 API 客户端仍期望不同的响应，则此功能可能很有用。 有关详细信息，请参阅[客户端功能](claims-challenge.md#client-capabilities)部分。

## <a name="caveats-and-recommendations"></a>注意事项和建议

请勿在应用中对身份验证上下文值进行硬编码。 应用应[使用 MS Graph 调用](/graph/api/resources/authenticationcontextclassreference)读取和应用身份验证上下文。 这种做法对于[多租户应用程序](howto-convert-app-to-be-multi-tenant.md)至关重要。 身份验证上下文值因 Azure AD 租户而异，在免费版 Azure AD 中不可用。 若要详细了解应用应如何在代码中查询、设置和使用身份验证上下文，请参阅代码示例[使用条件访问身份验证上下文进行逐步身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)，了解应用应如何在代码中查询、设置和使用身份验证上下文。 

对于本身将成为条件访问策略目标的应用，请勿使用身份验证上下文。 当应用程序的某些部分要求用户满足更高的身份验证标准时，此功能就很有用。

## <a name="next-steps"></a>后续步骤

- [对敏感数据和操作进行精细条件访问（博客）](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
- [零信任与 Microsoft 标识平台](/security/zero-trust/identity-developer)
- [使用 Microsoft 标识平台构建零信任就绪应用](/security/zero-trust/identity-developer)
- [使用条件访问身份验证上下文为 Web 应用中的高特权操作执行升级身份验证](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md)
- [使用条件访问身份验证上下文对 Web API 中的高特权操作进行逐步身份验证](https://github.com/Azure-Samples/ms-identity-ca-auth-context/blob/main/README.md)
- [条件访问身份验证上下文](../conditional-access/concept-conditional-access-cloud-apps.md#authentication-context-preview)
- [authenticationContextClassReference 资源类型 - MS Graph](/graph/api/conditionalaccessroot-list-authenticationcontextclassreferences)
- [Microsoft 标识平台中的声明质询、声明请求和客户端功能](claims-challenge.md)
- [将身份验证上下文与 Microsoft 信息保护和 SharePoint 结合使用](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
- [如何在应用程序中使用启用了连续访问评估的 API](app-resilience-continuous-access-evaluation.md)
