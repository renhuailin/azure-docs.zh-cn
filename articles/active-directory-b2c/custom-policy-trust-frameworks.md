---
title: Azure AD B2C 自定义策略概述 | Microsoft Docs
description: 本主题介绍了 Azure Active Directory B2C 自定义策略和标识体验框架。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 514ce0a43904048952f38edd6a9d38713f6ef8f3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936663"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Azure AD B2C 自定义策略概述

自定义策略是定义 Azure Active Directory B2C (Azure AD B2C) 租户行为的配置文件。 尽管 [用户流](user-flow-overview.md) 是在 Azure AD B2C 门户中为最常见的标识任务预定义的，但标识开发人员可以完全编辑自定义策略来完成许多不同的任务。

自定义策略是完全可配置和策略驱动的。 自定义策略协调标准协议格式（例如 OpenID Connect、OAuth、SAML 和一些非标准的实体）之间的信任关系，例如 REST API 的系统到系统的声明交换。 该框架创建用户友好的白标体验。

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义构建基块、与用户和其他方的交互以及业务逻辑。 

## <a name="custom-policy-starter-pack"></a>自定义策略新手包

Azure AD B2C 自定义策略[新手包](custom-policy-get-started.md#get-the-starter-pack)附带了几项预建策略，可助让你快速运行。 每个新手包中都有实现所述方案必需的最少数量的技术配置文件和用户旅程：

- **LocalAccounts** - 仅允许使用本地帐户。
- **SocialAccounts** - 仅允许使用社交（或联合）帐户。
- **SocialAndLocalAccounts** - 允许使用本地帐户和社交帐户。 我们的大多数示例都引用此策略。
- **SocialAndLocalAccountsWithMFA** - 启用社交、本地和多重身份验证选项。

## <a name="understanding-the-basics"></a>了解基础知识 

### <a name="claims"></a>声明

声明可在 Azure AD B2C 策略执行过程中提供数据的临时存储。 它可存储用户相关信息，例如名字、姓氏，或者从用户或其他系统获得的任何其他声明（声明交换）。 [声明架构](claimsschema.md)是发出声明的位置。 

当策略运行时，Azure AD B2C 会向内部方和外部方发送声明并或他们那里接收声明，然后将其中部分声明作为令牌的一部分发送到信赖方应用。 通过以下方式使用声明： 

- 针对目录用户对象保存、读取或更新声明。
- 从外部标识提供者那里接收声明。
- 使用自定义 REST API 服务发送或接收声明。
- 在注册或编辑配置文件流期间，以声明的形式从用户那里收集数据。

### <a name="manipulating-your-claims"></a>操作声明

[声明转换](claimstransformations.md)是预定义函数，可用于将给定声明转换为其他声明、评估声明或设置声明值。 例如，将项添加到字符串集合，更改字符串的大小写，或者计算日期和时间声明。 声明转换会指定一个转换方法。 

### <a name="customize-and-localize-your-ui"></a>自定义和本地化 UI

当你想要通过在用户的 Web 浏览器中显示页面，从用户那里收集信息时，请使用[自断言技术配置文件](self-asserted-technical-profile.md)。 您可以编辑您的自断言技术配置文件，以 [添加声明和自定义用户输入](./configure-user-input.md)。

若要为自断言技术配置文件[自定义用户界面](customize-ui-with-html.md)，请在包含自定义 HTML 内容的[内容定义](contentdefinitions.md)元素中指定一个 URL。 在自断言技术配置文件中，指向该内容定义 ID。

若要自定义语言特定的字符串，请使用[本地化](localization.md)元素。 内容定义可包含[本地化](localization.md)引用，它指定了要加载的本地化资源列表。 Azure AD B2C 将用户界面元素与从 URL 加载的 HTML 内容合并，然后向用户显示页面。 

## <a name="relying-party-policy-overview"></a>信赖方策略概述

在 SAML 协议中称为服务提供程序的信赖方应用程序调用 [信赖方策略](relyingparty.md) 来执行特定用户旅程。 信赖方策略会指定要执行的用户旅程，以及令牌包含的声明列表。 

![显示策略执行流的示意图](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

所有使用同一策略的信赖方应用程序都将收到相同的令牌声明，用户会经历相同的用户旅程。

### <a name="user-journeys"></a>用户旅程

通过[用户旅程](userjourneys.md)可定义具有路径的业务逻辑，用户将通过该路径访问你的应用程序。 用户将通过用户旅程检索要提供给应用程序的声明。 用户旅程是从一系列[业务流程步骤](userjourneys.md#orchestrationsteps)构建的。 用户必须到达最后一步才能获取令牌。 

以下说明介绍了如何将业务流程步骤添加到 [社交和本地帐户初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) 策略中。 下面是已添加的 REST API 调用的示例。

![自定义用户旅程](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>业务流程步骤

业务流程步骤会引用实现其预期目的或功能的方法。 此方法称为[技术配置文件](technicalprofiles.md)。 当用户旅程需要进行分支才能更好地表示业务逻辑时，业务流程步骤将引用[子旅程](subjourneys.md)。 子旅程包含自己的一组业务流程步骤。

用户必须到达用户旅程中的最后一个业务流程步骤才能获取令牌。 但用户可能不需要遍历所有业务流程步骤。 业务流程步骤可基于业务流程步骤中定义的[前提条件](userjourneys.md#preconditions)有条件地执行。 

业务流程步骤完成后，Azure AD B2C 会将输出的声明存储在“声明包”中。 声明包中的声明可由用户旅程中的任何其他业务流程步骤使用。

下图显示了用户旅程的业务流程步骤如何访问声明包。

![Azure AD B2C 用户旅程](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>技术配置文件

技术配置文件提供了与不同类型的参与方进行通信的接口。 用户旅程通过业务流程步骤结合调用技术配置文件来定义业务逻辑。

所有类型的技术配置文件都具有相同的概念。 可发送输入声明、运行声明转换，并与配置的参与方通信。 在该过程完成后，技术配置文件会将输出声明返回到声明包。 有关详细信息，请参阅 [技术配置文件概述](technicalprofiles.md)。

### <a name="validation-technical-profile"></a>验证技术配置文件

当用户与用户界面交互时，可能需要验证所收集的数据。 若要与用户交互，必须使用[自断言技术配置文件](self-asserted-technical-profile.md)。

若要验证用户输入，请从自断言技术配置文件中调用 [验证技术配置文件](validation-technical-profile.md) 。 验证技术配置文件是调用任何非交互式技术配置文件的方法。 在此情况下，技术配置文件可返回输出声明，也可能会返回错误消息。 错误消息在屏幕上呈现给用户，允许用户重试。

下图说明了 Azure AD B2C 如何使用验证技术配置文件来验证用户凭据。

![验证技术配置文件示意图](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>继承模型

每个新手包都包含以下文件：

- **基本** 文件：包含大多数定义。 为了帮助进行故障排除和长期维护策略，请尝试最大程度地减少对此文件所做的更改的数量。
- **扩展** 文件：保存租户的独特配置更改。 此策略文件派生自“基本”文件。 使用此文件可以添加新功能或替代现有功能。 例如，使用此文件可与新的标识提供者联合。
- **信赖方 (RP)** 文件：注重单个任务的文件，由信赖方应用程序（例如 Web、移动或桌面应用程序）直接调用。 每项独特的任务（例如注册、登录、密码重置或配置文件编辑）都需要自身的信赖方策略文件。 此策略文件派生自“扩展”文件。

继承模型如下所示：

- 任何级别的子策略可以继承自父策略，并通过添加新元素来扩展父策略。
- 对于更复杂的方案，你可以添加更多的继承级别 (最多5个) 。
- 你可添加更多的信赖方策略。 例如，删除我的帐户、更改电话号码、SAML 信赖方策略等等。

下图显示了策略文件与信赖方应用程序之间的关系。

![显示信任框架策略继承模型的示意图](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>指南和最佳做法

### <a name="best-practices"></a>最佳做法

在 Azure AD B2C 自定义策略中，你可以集成你自己的业务逻辑来构建你需要的用户体验，并扩展该服务的功能。 我们有一套最佳做法和建议可助你入门。

- 在 **扩展策略** 中创建逻辑，或在 **信赖方策略** 中创建逻辑。 可添加新元素，这将引用相同的 ID 来覆盖基本策略。 这样，你就可横向扩展项目，同时使得稍后在 Microsoft 发布新的新手包时能轻松地升级基本策略。
- 在“基本策略”中，强烈建议避免进行任何更改。 如有必要，请在进行更改时发出注释。
- 重写元素（例如技术配置文件元数据）时，请不要从基本策略复制整个技术配置文件。 相反，只复制元素的必需部分。 有关如何进行更改的示例，请参阅[禁用电子邮件验证](./disable-email-verification.md)。
- 若要减少共享核心功能的技术配置文件出现重复的情况，请使用[技术配置文件包含](technicalprofiles.md#include-technical-profile)。
- 不要在登录期间写入 Azure AD 目录，这可能会导致限制问题。
- 如果策略具有外部依赖项（如 REST Api），请确保它们具有高可用性。
- 为了提高用户体验，请确保使用[联机内容交付](../cdn/index.yml)来全局部署自定义 HTML 模板。 Azure 内容分发网络 (CDN) 使你可以减少加载时间、节省带宽和提高响应速度。
- 如果要对用户旅程做出更改，请将整个用户从基本策略迁移到扩展策略。 向已复制的用户旅程提供唯一的用户旅程 ID。 然后，在[信赖方策略](relyingparty.md)中，将[默认用户旅程](relyingparty.md#defaultuserjourney)元素更改为指向新的用户旅程。

## <a name="troubleshooting"></a>疑难解答

使用 Azure AD B2C 策略进行开发时，在执行用户旅程时可能会遇到错误或异常。 可使用 Application Insights 来调查这些问题。

- 将 Application Insights 与 Azure AD B2C 集成来[诊断异常](troubleshoot-with-application-insights.md)。
- [Visual Studio Code 的 Azure AD B2C 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)可帮助你根据策略名称和时间访问和[可视化日志](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md)。
- 设置自定义策略时最常见的错误是设置了不正确的 XML 格式。 在上传 XML 文件之前，请使用 [XML 架构验证](troubleshoot-custom-policies.md)来识别错误。

## <a name="continuous-integration"></a>持续集成

通过使用在 Azure Pipelines 中设置的 CI/CD) 管道 (持续集成和交付功能，你可以在软件传递和代码控制自动化 [中包含 Azure AD B2C 自定义策略](deploy-custom-policies-devops.md) 。 部署到不同的 Azure AD B2C 环境（例如开发、测试和生产环境）时，建议删除手动过程，并使用 Azure Pipelines 执行自动化测试。

## <a name="prepare-your-environment"></a>准备环境

Azure AD B2C 自定义策略入门：

1. [创建 Azure AD B2C 租户](tutorial-create-tenant.md)
1. 使用 Azure 门户[注册 web 应用程序](tutorial-register-applications.md)，以便能够测试策略。
1. 添加必要的 [策略密钥](custom-policy-get-started.md#add-signing-and-encryption-keys) 并 [注册标识体验框架应用程序](custom-policy-get-started.md#register-identity-experience-framework-applications)。
1. [获取 Azure AD B2C 策略新手包](custom-policy-get-started.md#get-the-starter-pack)并上传到你的租户。 
1. 上传 starter pack 后，请 [测试注册或登录策略](custom-policy-get-started.md#test-the-custom-policy)。
1. 建议下载并安装 [Visual Studio Code](https://code.visualstudio.com/) (VS Code)。 Visual Studio Code 是一种轻型但功能强大的源代码编辑器，可在桌面上运行，并且可用于 Windows、macOS 和 Linux。 使用 VS Code，可以通过安装[适用于 VS Code 的 Azure AD B2C 扩展](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)，快速导航并编辑 Azure AD B2C 自定义策略 XML 文件
 
## <a name="next-steps"></a>后续步骤

设置并测试 Azure AD B2C 策略后，可开始自定义策略。 阅读以下文章了解如何：

- [添加声明并使用自定义策略自定义用户输入](./configure-user-input.md) 。 了解如何通过自定义一些 starter pack 技术配置文件来定义声明并向用户界面添加声明。
- 使用自定义策略[自定义应用程序的用户界面](customize-ui-with-html.md)。 了解如何创建自己的 HTML 内容并自定义内容定义。
- 使用自定义策略[本地化应用程序的用户界面](./language-customization.md)。 了解如何添加本地化资源元素来设置支持的语言列表并提供特定于语言的标签。
- 在策略开发和测试期间，可以 [禁用电子邮件验证](./disable-email-verification.md)。 了解如何覆盖技术配置文件元数据。
- 使用自定义策略[设置使用 Google 帐户进行登录](./identity-provider-google.md)。 了解如何使用 OAuth2 技术配置文件创建新的声明提供程序。 然后自定义用户旅程以包含 Google 登录选项。
- 若要诊断自定义策略的问题，可[使用 Application Insights 收集 Azure Active Directory B2C 日志](troubleshoot-with-application-insights.md)。 了解如何添加新的技术配置文件，以及如何配置信赖方策略。
