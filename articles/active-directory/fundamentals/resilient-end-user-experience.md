---
title: 使用 Azure AD B2C 构建可复原的最终用户体验 | Microsoft Docs
description: 使用 Azure AD B2C 在最终用户体验中构建复原能力的方法
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
ms.openlocfilehash: f6896a812ec173994a1299a28ff2e99a0f351391
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724435"
---
# <a name="resilient-end-user-experience"></a>可复原的最终用户体验

注册和登录最终用户体验由以下几个元素组成：

- 用户与之交互的接口（如 CSS、HTML 和 JavaScript）

- 用户流和你创建的自定义策略-例如注册、登录和配置文件编辑

- 标识提供程序 (应用程序的 Idp) -例如本地帐户用户名/密码、Outlook、Facebook 和 Google

![图像显示了最终用户体验组件](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>在用户流和自定义策略之间进行选择  

为帮助你设置最常见的标识任务，Azure AD B2C 提供了内置的可配置[用户流](../../active-directory-b2c/user-flow-overview.md)。 你还可以构建你自己的[自定义策略](../../active-directory-b2c/custom-policy-overview.md)，这些策略为你提供最大的灵活性。 但是，建议你仅使用自定义策略来处理复杂的情况。

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>如何在用户流与自定义策略之间做出选择

如果内置用户流可以满足你的业务需求，请选择内置用户流。 由于 Microsoft 进行了广泛的测试，因此你可以最大限度地减少验证这些标识用户流的策略级功能、性能或缩放所需的测试。 你仍需测试应用程序的功能、性能和缩放。

由于你的业务要求，你应 [选择自定义策略](../../active-directory-b2c/custom-policy-get-started.md) ，请确保对功能、性能或规模以及应用程序级别的测试执行策略级测试。

请参阅[比较用户流和自定义策略](../../active-directory-b2c/custom-policy-overview.md#comparing-user-flows-and-custom-policies)一文来做出决定。

## <a name="choose-multiple-idps"></a>选择多个 IDP

当使用 [外部标识提供者](../../active-directory-b2c/technical-overview.md#external-identity-providers) （如 Facebook）时，请确保在外部提供商不可用的情况下获得备用计划。

### <a name="how-to-set-up-multiple-idps"></a>如何设置多个 IDP

在注册外部标识提供者的过程中，请包括已验证的标识声明，例如用户的手机号码或电子邮件地址。 将已验证的声明提交到基础 Azure AD B2C 目录实例。 如果外部提供者不可用，则恢复使用已验证的标识声明，并回退到使用电话号码作为身份验证方法。 另一个选项是向用户发送一次性密码，以允许用户登录。

 请按照以下步骤[生成备用身份验证路径](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)：

 1. 配置你的注册策略，以允许通过本地帐户和外部 IDP 进行注册。

 2. 配置一个配置文件策略，以允许用户在登录后[将其他标识关联到帐户](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking)。

 3. 通知并允许用户在中断期间 [切换到备用 IDP](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 。

## <a name="availability-of-multi-factor-authentication"></a>多重身份验证的可用性

使用 [电话服务进行多重身份验证时 (MFA) ](../../active-directory-b2c/phone-authentication.md)，请务必考虑备选服务提供商。 本地电信或电话服务提供商可能会在其服务中遇到中断。

### <a name="how-to-choose-an-alternate-mfa"></a>如何选择备用 MFA  

Azure AD B2C 服务使用内置的基于电话的 MFA 提供程序，以提供基于时间的一次性密码 (OTPs) 。 它以语音呼叫和短信形式发送给用户的预注册电话号码。 以下替代方法适用于各种方案：

使用 **用户流** 时，有两种方法可生成复原能力：

- **更改用户流配置**：检测到基于手机的 OTP 传递中的中断后，请将 OTP 传递方法从电话更改为基于电子邮件的，并重新部署用户流，使应用程序保持不变。

![屏幕截图显示登录注册](media/resilient-end-user-experiences/create-sign-in.png)

- **更改应用程序**：对于每个标识任务（例如，注册和登录），定义两组用户流。 将第一个设置为使用基于电话的 OTP，将第二个设置为使用基于电子邮件的 OTP。 检测到基于手机的 OTP 传递中断后，更改和重新部署应用程序，使其从第一组用户流切换到第二组，使用户保持不变。  

使用 **自定义策略** 时，有四种方法可生成复原能力。 下面列出了复杂性，需要重新部署更新的策略。

- **允许用户选择基于电话的 otp 或基于电子邮件的 otp**：向用户公开两个选项，并使用户能够自行选择其中一个选项。 不需要对策略或应用程序进行更改。

- 在 **基于电话的 otp 和基于电子邮件的 otp 之间动态切换**：注册时收集电话和电子邮件信息。 提前定义自定义策略，以有条件地在电话中断期间切换，从电话到基于电子邮件的 OTP 交付。 不需要对策略或应用程序进行更改。

- **使用验证器应用**：更新自定义策略以使用 [验证器应用](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)。 如果你的普通 MFA 是基于电话或基于电子邮件的 OTP，则重新部署自定义策略以切换为使用验证器应用。

>[!Note]
>用户需要在注册过程中配置验证器应用集成。

- **使用安全问题**：如果上述任何方法均不适用，请将安全问题作为备份进行实施。 在载入或配置文件编辑过程中为用户设置安全问题，并将答案存储在目录以外的其他数据库中。 此方法并不满足 "你有什么" 的 MFA 要求，例如 "电话"，但提供辅助 "了解你知道的内容"。

## <a name="use-a-content-delivery-network"></a>使用内容分发网络

内容分发网络 (CDN) 具有更好的性能，并且用来存储自定义用户流 UI 比 Blob 存储更便宜。 网页内容将通过地理上分布的高可用性服务器网络更快地分发。  

请定期通过端到端方案和负载测试来测试你的 CDN 的可用性和内容分发性能。 如果为即将到来的流量激增（由于促销或假日流量）进行规划，请修改你用于负载测试的估计值。
  
## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原能力资源](resilience-b2c.md)
  
  - [与外部进程建立可复原接口](resilient-external-processes.md)
  - [通过开发人员最佳做法实现复原能力](resilience-b2c-developer-best-practices.md)
  - [通过监视和分析实现复原能力](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)