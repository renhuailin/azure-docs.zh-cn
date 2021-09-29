---
title: Twilio Verify 应用和 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 中的示例在线支付应用与 Twilio Verify API 集成。 通过动态链接和强大的客户身份验证，满足 PSD2（付款服务指令 2）事务要求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6937318e3d2e8d1a42279242a0d8b476cdab892f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606677"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>将 Twilio Verify 应用与 Azure Active Directory B2C 集成

在本次演练中，了解如何将 Azure Active Directory B2C (Azure AD B2C) 中的示例在线支付应用与 Twilio Verify API 集成。 通过使用 Twilio Verify 应用，Azure AD B2C 客户可以通过动态链接和强大的客户身份验证满足 PSD2（付款服务指令 2）事务要求。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。
* Twilio 上的[试用帐户](https://www.twilio.com/try-twilio)。

## <a name="scenario-description"></a>方案描述

以下组件组成了 Twilio 解决方案：

- .NET [PSD2 演示 Web 应用](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)，提供登录或注册功能，并执行一个虚拟高风险事务。
- Azure AD B2C 合并的[登录和注册策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)。
- 使用 `id_token_hint` 与 Twilio Verify API 集成的 Azure AD B2C 策略。
- .NET Web 应用，该应用承载 `.well-known`OpenIdConnect 终结点以允许验证 `id_token_hint`。


    ![twilio 流](media/partner-twilio/twilio-flow.png)

| 步骤 | 说明 |
|------|------|
| 1     | 用户启动登录或注册到 PSD2 演示应用。 用户通过 Azure AD B2C 合并的登录和注册策略进行身份验证。 令牌返回到应用程序。 注册时，用户的电话号码使用短信/电话进行验证，并记录在其 Azure AD B2C 帐户上。     |
| 2     | 用户启动高风险事务，例如转账 $50.00。 对用户当前访问令牌的 PolicyId 进行评估，以确定用户是否已通过“升级”自定义策略进行身份验证。     |
| 3     | 应用程序记录交易值 $50.00 和收款人 John Doe，并生成已签名的令牌。 此令牌称为 `id_token_hint`，并包含声明 `amount:$500, payee:john doe`。 `id_token_hint` 与请求一起发送到 Azure AD B2C 自定义策略，该策略与 Twilio 集成。     |
| 4     | Azure AD B2C 通过检查应用程序 `/.well-known` OpenId Connect 终结点来验证 id_token_hint 的签名。 验证后，将提取此令牌中的声明，尤其是 `amount` 和 `payee`。 用户将看到一个页面，以通过短信验证其移动电话号码。     |
| 5     | 用户请求通过短信验证其电话号码，Azure AD B2C 则向 Twilio 的验证 API 终结点发出 REST API 请求。 它还会发送事务 `amount` 和 `payee` 作为 PSD2 过程的一部分，以生成一次性密码 (OTP)。 Twilio 将向用户已注册的电话号码发送一条短信。     |
| 6     |  用户输入在其短信中收到的 OTP，并将其提交到 Azure AD B2C。 Azure AD B2C 使用此 OTP 向 Twilio 的验证 API 发出 API 请求，以验证 OTP 是否正确。 最后，向应用程序颁发令牌，其中包含的新 PolicyId 表明用户已升级身份验证。    |
|      |      |

## <a name="onboard-with-twilio"></a>加入 Twilio

1. 在 Twilio 获取[试用帐户](https://www.twilio.com/try-twilio)。

1. 按照[本文](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)中所述，在 Twilio 购买电话号码

1. 在 Twilio 控制台导航到[验证 API](https://www.twilio.com/console/verify/services)，并按照[说明](https://www.twilio.com/docs/verify/verifying-transactions-psd2)创建服务并启用 PSD2 选项。  

## <a name="configure-the-psd2-demo-app"></a>配置 PSD2 演示应用

1. 打开 B2C-WebAPI-DotNet 解决方案，并将以下值替换为 web.config 中你自己的特定于租户的值：

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

1. [Web 应用](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)还承载 ID 令牌提示生成器和元数据终结点。
   - 按照此[示例说明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)中所述创建签名证书。
   - 根据 web.config 中的证书更新以下行：
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

1. 将演示应用程序上传到所选的托管提供程序。  [本示例说明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)中提供了 Azure 应用服务指南，其中包括证书上传说明。

1. 通过添加与托管应用程序的 URL 等效的回复 URL 来更新 Azure AD B2C 应用程序注册。

1. 打开[策略文件](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)，将  `contoso` 的所有实例替换为你的租户名。

1. 查找 Twilio REST API 技术配置文件“Custom-SMS-Enroll” **** 。 使用你的 Twilio AccountSID 更新  `ServiceURL` ，并将致电人号码更新为你购买的电话号码。

1. 查找 Twilio REST API 技术配置文件“TwilioRestAPI-Verify-Step1” ****  和“TwilioRestAPI-Verify-Step2” **** ，并使用你的 Twilio AccountSID 更新  `ServiceURL` 。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

将策略文件添加到 Azure AD B2C：

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 导航到“Azure AD B2C” > “Identity Experience Framework” > “策略密钥”。
1. 添加名为“B2cRestTwilioClientId”的新密钥。 选择“手动” **** ，并提供 Twilio AccountSID 的值。
1. 添加名为“B2cRestTwilioClientSecret”的新密钥。 选择“手动” **** ，并提供 Twilio 身份验证令牌的值。
1. 将所有策略文件上传到你的租户。
1. 为你的注册短信自定义 GenerateOTPMessageEnrol 声明转换中的字符串。

## <a name="test-the-solution"></a>测试解决方案

* 浏览到应用程序，并测试登录、注册和汇款操作。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- 有关 [Twilio 集成代码示例](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)，请参阅 GitHub  

- [AAD B2C 中的自定义策略](custom-policy-overview.md)

- [AAD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
