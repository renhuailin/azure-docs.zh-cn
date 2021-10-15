---
title: 为应用设置测试环境
titleSuffix: Microsoft identity platform
description: 了解如何设置 Azure Active Directory 测试环境，以便可以测试与 Microsoft 标识平台集成的应用程序。  评估是否需要使用单独的租户进行测试，或者是否可以使用生产租户。
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2021
ms.author: arcrowe
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e695a7028432b1044c0c1896447d474b2716097f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367369"
---
# <a name="set-up-your-applications-azure-ad-test-environment"></a>设置应用程序的 Azure AD 测试环境

作为开发人员，你可以使用软件开发生命周期并在开发、测试和生产环境之间移动应用。  将此过程用于受 Microsoft 标识平台保护的应用程序时，应设置一个用于测试的 Azure Active Directory (Azure AD) 环境。  可以在开发生命周期的早期测试阶段中以及长期的永久测试环境中使用此环境。  根据资源隔离要求，可以使用组织的 Azure AD 生产租户或完全独立的租户进行测试。  

本文介绍如何设置 Azure AD 测试环境，以便可以测试与 Microsoft 标识平台集成的应用程序。  评估所需的隔离级别，是否需要使用单独的租户进行测试，或者是否可以使用生产租户。

## <a name="decide-the-level-of-isolation-needed"></a>确定所需的隔离级别
一般情况下，将生产租户用作测试环境会更方便且开销更少。 但是，仅当可以在测试和生产资源之间实现适当的隔离级别时，这种做法才可行。  隔离对于高特权方案尤其重要。

对于以下情况，请在单独的租户（而不是组织的生产租户）中设置测试环境：
- 你的一组资源需要租户范围的独特设置。 例如，你的应用可能需要代表自身而不是代表某个用户（使用仅限应用的权限）访问租户资源。  仅限应用的访问需要在整个租户中应用的管理员同意，而这些权限很难在租户边界内安全地缩小范围。
- 你对租户成员未经授权访问测试资源所带来的风险的承受能力极低。
- 配置更改可能会对生产环境造成严重影响。
- 你无法在租户中创建测试用户和关联的测试数据。
- 你打算向应用程序执行自动登录以进行测试，而生产租户配置了需要进行一些用户交互的身份验证方法策略。  例如，如果所有用户都需要完成多重身份验证，则你无法执行自动登录来进行集成测试。
- 必须确保全局管理员无法管理或访问特定的测试资源。 需要在单独的全局管理员的配合下，在单独的租户中隔离该资源。
- 将非生产资源和/或工作负载添加到生产租户会超过该租户的[服务限制或请求限制](test-throttle-service-limits.md)。

如果这些条件都不适用于你，请按照这些步骤[在生产租户中设置测试环境](#set-up-a-test-environment-in-your-production-tenant)。  但是，如果其中有任何一个条件适用，则应该[在单独的租户中设置测试环境](#set-up-a-test-environment-in-a-separate-tenant)。

## <a name="set-up-a-test-environment-in-a-separate-tenant"></a>在单独的租户中设置测试环境
如果无法在生产租户中安全地约束测试应用程序，可以创建一个单独的租户用于开发和测试。 

### <a name="get-a-test-tenant"></a>获取测试租户
如果你还没有专用的测试租户，可以使用 Microsoft 365 开发人员计划免费创建或自行手动创建一个测试租户。 

#### <a name="join-the-microsoft-365-developer-program-recommended"></a>加入 Microsoft 365 开发人员计划（建议） 
[Microsoft 365 开发人员计划](https://developer.microsoft.com/microsoft-365/dev-program)是免费的，使用它可将测试用户帐户和示例数据包自动添加到租户。
1. 单击屏幕上的“立即加入”按钮。
2. 使用新的 Microsoft 帐户或现有（工作）帐户登录。
3. 在登录页面，选择所在区域，输入公司名称并接受计划相关的条款和条件，然后单击“下一步”。
4. 单击“设置订阅”。 指定要在其中创建新租户的区域，并创建用户名、域，然后输入密码。 这样即可创建一个新租户和该租户的第一个管理员。
5. 输入保护新租户的管理员帐户所需的安全信息。 这将为帐户设置多重身份验证

#### <a name="manually-create-a-tenant"></a>手动创建租户  
可以[手动创建一个租户](quickstart-create-new-tenant.md)，该租户最初是空的，必须使用测试数据对其进行配置。

### <a name="populate-your-tenant-with-users"></a>在租户中填充用户  
为方便起见，你可能希望邀请自己和其他开发团队成员作为租户中的来宾用户。  这会在测试租户中创建单独的来宾对象，但也意味着，你只需为企业帐户和测试帐户管理一组凭据。
1. 在 [Azure 门户](https://portal.azure.com)中，单击“Azure Active Directory”。
2. 转到“用户”  。
3. 单击“新建来宾用户”并邀请你的工作帐户电子邮件地址。
4. 对应用程序的开发和/或测试团队的其他成员重复此操作。

还可以在测试租户中创建测试用户。  如果你使用了 Microsoft 365 示例包之一，则租户中可能已有一些测试用户。  否则，你应该能够以租户管理员的身份自行创建一些测试用户。
1. 在 [Azure 门户](https://portal.azure.com)中，单击“Azure Active Directory”。
2. 转到“用户”  。
3. 单击“新建用户”并在目录中创建一些新的测试用户。

### <a name="get-an-azure-ad-subscription-optional"></a>获取 Azure AD 订阅（可选）
若要在应用程序上全面测试 Azure AD 高级功能，需要为租户注册[高级 P1 或高级 P2 许可证](https://azure.microsoft.com/pricing/details/active-directory/)。

如果你已使用 Microsoft 365 开发人员计划进行注册，则测试租户将会附带 Azure AD P2 许可证。  否则，你仍可以启用[免费试用一个月的 Azure AD 高级版](https://azure.microsoft.com/trial/get-started-active-directory/)。

### <a name="create-and-configure-an-app-registration"></a>创建和配置应用注册
需要创建一个要在测试环境中使用的应用注册。  该注册应该不同于最终的生产应用注册，以便在测试环境和生产环境之间保持安全隔离。  配置应用程序的方式取决于所要生成的应用的类型。  有关详细信息，请在左侧导航窗格中查看适用于你的应用方案的应用注册步骤，如这篇有关 [Web 应用程序注册](scenario-web-app-sign-user-app-registration.md)的文章中所述。
   
### <a name="populate-your-tenant-with-policies"></a>在租户中填充策略
如果应用主要由单个组织（通常称为单租户）使用，并且你有权访问该生产租户，那么，应该尝试复制可能影响应用行为的生产租户设置。  这可以降低在生产环境中操作时出现意外错误的可能性。

#### <a name="conditional-access-policies"></a>条件性访问策略 
复制条件访问策略可确保在转移到生产环境时不会遇到意外的阻止访问错误，并且应用程序可以适当处理它可能会收到的错误。

查看生产租户条件访问策略的操作可能需要由公司管理员来执行。  
1. 使用你的生产租户帐户登录到 [Azure 门户](https://portal.azure.com)
1. 转到“Azure Active Directory” > “企业应用程序” > “条件访问”  。
1. 查看租户中的策略列表。  单击第一个策略。
1. 导航到“云应用或操作”。  
1. 如果该策略仅应用于选定的一组应用，请转到下一个策略。  如果不是，在你转到生产环境时，该策略也可能会应用于你的应用。  应将该策略复制到测试租户。

在新选项卡或浏览器会话中，导航到 [Azure 门户](https://portal.azure.com)并登录到测试租户。
1. 转到“Azure Active Directory” > “企业应用程序” > “条件访问”  。
1. 单击“新建策略”
1. 复制通过前面的步骤识别的生产租户策略中的设置。

#### <a name="permission-grant-policies"></a>权限授予策略
复制权限授予策略可确保在转移到生产环境时不会遇到意外的管理员同意提示。 

1. 使用你的生产租户帐户登录到 [Azure 门户](https://portal.azure.com)
1. 单击“Azure Active Directory”。
1. 转到“企业应用程序”。 
1. 从生产租户转到“Azure Active Directory” > “企业应用程序” > “同意和权限” > “用户同意设置”   。  将其中的设置复制到测试租户。  
    
#### <a name="token-lifetime-policies"></a>令牌生存期策略
复制令牌生存期策略可确保颁发给应用程序的令牌不会在生产环境中意外过期。  
 
目前只能通过 PowerShell 管理令牌生存期策略。 阅读[可配置令牌生存期](active-directory-configurable-token-lifetimes.md)，了解如何识别应用于整个生产组织的任何令牌生存期策略。  将这些策略复制到测试租户。
 
## <a name="set-up-a-test-environment-in-your-production-tenant"></a>在生产租户中设置测试环境
如果可以安全地在生产租户中约束测试应用，请继续设置租户以进行测试。

### <a name="create-and-configure-an-app-registration"></a>创建和配置应用注册
需要创建一个要在测试环境中使用的应用注册。  该注册应该不同于最终的生产应用注册，以便在测试环境和生产环境之间保持安全隔离。  配置应用程序的方式取决于所要生成的应用的类型。  有关详细信息，请在左侧导航窗格中查看[适用于你的应用方案的应用注册步骤](scenario-web-app-sign-user-app-registration.md)。

### <a name="create-some-test-users"></a>创建一些测试用户
需要创建一些具有关联测试数据的测试用户，以便在测试方案时使用。  此步骤可能需要由管理员执行
1. 在 [Azure 门户](https://portal.azure.com)中，单击“Azure Active Directory”。
2. 转到“用户”  。
3. 单击“新建用户”并在目录中创建一些新的测试用户。

### <a name="add-the-test-users-to-a-group-optional"></a>将测试用户添加到组中（可选）
为方便起见，可以将所有这些用户分配到某个组中，使其他分配操作变得更容易。  
1. 在 [Azure 门户](https://portal.azure.com)中，单击“Azure Active Directory”。
2. 转到“组”。
3. 单击“新建组”。
4. 选择“安全性”或“Microsoft 365”作为组类型 。
5. 为该组命名。
6. 添加在上一步骤中创建的测试用户。

### <a name="restrict-your-test-application-to-specific-users"></a>仅限特定的用户使用测试应用程序
可以通过用户分配，将租户中有权使用测试应用程序的用户限制为特定的用户或组。  当你[通过应用注册创建应用](#create-and-configure-an-app-registration)时，也已在“企业应用程序”中创建了该应用的表示形式。  使用“企业应用程序”设置来限制谁可以在租户中使用该应用程序。

> [!IMPORTANT]
> 如果你的应用是[多租户应用](v2-supported-account-types.md)，则此操作不会限制其他租户中的用户登录和使用你的应用。  它只会限制在其中配置了用户分配的租户中的用户。 

有关仅限租户中的特定用户使用应用的详细说明，请转到[仅限一组用户使用你的应用](howto-restrict-your-app-to-a-set-of-users.md)。

## <a name="next-steps"></a>后续步骤
 
了解在设置测试环境时可能会遇到的[请求限制和服务限制](test-throttle-service-limits.md)。

有关测试环境的更详细信息，请阅读[使用 Azure Active Directory 保护 Azure 环境](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)。
  
