---
title: 将所有应用与 Azure AD 集成的五个步骤
description: 本指南说明如何将所有应用程序与 Azure AD 集成。 在每个步骤中，我们将解释值，并提供解释技术详细信息的资源链接。
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7c3e4441289b2b17cf772df73f3c53caa465813e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732587"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>将所有应用与 Azure AD 集成的五个步骤

Azure Active Directory (Azure AD) 是 Microsoft 基于云的标识和访问管理服务。 Azure AD 提供安全的身份验证和授权解决方案，使客户、合作伙伴和员工可以访问所需的应用程序。 通过 Azure AD、[条件访问](../conditional-access/overview.md)、[多重身份验证](../authentication/concept-mfa-howitworks.md)、[单一登录](../hybrid/how-to-connect-sso.md)和[自动用户预配](../app-provisioning/user-provisioning.md)，可轻松、安全地进行标识和访问管理。

如果你的公司有 Microsoft 365 订阅，你可能[已经使用](/office365/enterprise/about-office-365-identity) Azure AD。 不过，Azure AD 可用于所有应用程序，通过[集中应用程序管理](../manage-apps/common-scenarios.md)，你可以跨整个应用程序组合使用相同的标识管理功能、工具和策略。 这样将提供一个统一的解决方案，可提高安全性、降低成本、提高工作效率，并可确保合规性。 你将获得对本地应用的远程访问权限。

本指南说明如何将所有应用程序与 Azure AD 集成。 在每个步骤中，我们将解释值，并提供解释技术详细信息的资源链接。 我们按建议的顺序介绍这些步骤。 但你可以跳转到过程的任何部分，以开始学习对你最重要的部分。

可以在[将应用程序迁移到 Azure Active Directory](../manage-apps/migration-resources.md) 页面的资源中找到本主题中的其他资源（包括内容深入的业务流程白皮书）。

## <a name="1-use-azure-ad-for-new-applications"></a>1.1. 将 Azure AD 用于新应用程序

首先，重点关注新获取的应用程序。 你的业务开始使用新的应用程序时，请立即[将其添加到 Azure AD 租户](../manage-apps/add-application-portal.md)。 制定公司策略，规定将新应用添加到 Azure AD 作为组织中的标准做法。 这对现有业务流程的影响程度最低，并且让你能够调查并证明从集成应用程序中获得的价值，无需改变用户在当今环境中开展业务的方式。

Azure Active Directory (Azure AD) 有一个库，其中包含数千预集成的应用程序，因此可以轻松入门。 遵循与各种流行应用进行集成的分步操作[教程](../saas-apps/tutorial-list.md)，可以[将库应用添加到 Azure AD 组织](../manage-apps/add-application-portal.md)，这些应用包括：

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

此外，还可以[集成库中未包含的应用程序](../manage-apps/view-applications-portal.md)，包括已存在于组织中的任何应用程序或来自供应商的任何第三方应用程序（尚不在 Azure AD 库中）。 如果应用不在库中，也可以[将应用添加到库中](../develop/v2-howto-app-gallery-listing.md)。

最后，你还可以集成内部开发的应用。 这将在本指南的第 5 步中介绍。

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2.2. 确定现有应用程序使用情况并排定工作优先级

接下来，发现员工经常使用的应用程序，并优先考虑将这些应用程序与 Azure AD 集成。

首先可以使用 Microsoft Cloud App Security [云发现工具](/cloud-app-security/tutorial-shadow-it)，以发现和管理网络中的影子 IT（即不受 IT 部门管理的应用）。 你可以[使用 Microsoft Defender 高级威胁防护 (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 来简化和扩展发现过程。

此外，还可以使用 Azure 门户中的 [AD FS 应用程序活动报表](../manage-apps/migrate-adfs-application-activity.md)来发现组织中的所有 AD FS 应用、登录到这些应用的唯一用户的数量，以及将其与 Azure AD 集成的兼容性。

发现了现有布局后，你将需要[创建一个计划](../manage-apps/migration-resources.md)并确定要集成的最高优先级应用。 下面这些示例问题可以指导你完成该过程：

- 哪些应用最常使用？
- 哪些应用最危险？
- 将来哪些应用会被停用，因此没有必要迁移？
- 哪些应用需要保留在本地，不能迁移到云中？

在所有应用程序集成后，你将看到最大的益处和成本节约，并且不再依赖多个标识解决方案。 但是，当你将逐步实现此目标时，你将体验到更轻松的身份管理和提高安全性。 你希望利用这段时间来确定工作优先级，并判断自己的情况。

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3.集成依赖于其他标识提供者的应用

在发现过程中，你可能找到了 IT 部门未跟踪的应用程序，这些应用程序会让你的数据和资源容易受到攻击。 你的某些应用程序还还可能使用替代标识解决方案，包括 Active Directory 联合身份验证服务 (ADFS) 或其他标识提供者。 考虑如何合并标识和访问管理，以节省资金并提高安全性。 减少标识解决方案的数量后，你将获得以下优势：

- 由于无需本地用户预配和身份验证，也无需为同一服务的其他云标识提供者支付许可费用，因此可以为你节省资金。
- 减少标识和访问管理过程中的冗余，因此降低管理开销并实现更严格的安全性。
- 使员工能够通过 [MyApps 门户](../manage-apps/access-panel-collections.md)安全地对所需的所有应用程序进行单一登录访问。
- 通过增加从应用使用情况获取的数据量来改善 Azure AD 的[标识保护](../identity-protection/overview-identity-protection.md)相关服务（如条件性访问）的智能程度，并将其优势扩展到新添加的应用。

我们发布了有关如何管理将应用与 Azure AD 集成的业务流程的指南，包括[帖文](https://aka.ms/AppOnePager)和[演示](https://aka.ms/AppGuideline)以便使业务和应用程序所有者入门并产生兴趣。 你可以使用自己的品牌标记修改这些示例，并在完成此过程时通过公司门户、新闻稿或其他介质将它们发布到组织。

一个不错的起点是评估你对 Active Directory 联合身份验证服务 (ADFS) 的使用情况。 许多组织使用 ADFS 向 SaaS 应用、自定义业务线应用和 Microsoft 365 和基于 Azure AD 的应用进行身份验证：

![该关系图显示本地应用、业务线应用、SaaS 应用，并且显示了通过 Azure AD，Office 365 连接到 Active Directory 和 AD FS（以虚线表示）。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

可以通过将 [ADFS 替换为标识管理解决方案的中心作为 Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md)来升级此配置。 除了上述其他权益之外，这样做还可以为你的员工要访问的每个应用启用登录，并使员工可以通过 [MyApps 门户](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)轻松找到所需的任何业务应用程序。

![该关系图显示了通过 Active Directory 和 AD FS 的本地应用、业务线应用、SaaS 应用和 Office 365，所有应用都连接到 Azure Active Directory（以虚线表示）。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

一旦 Azure AD 成为中心标识提供者，你就可以从 ADFS 完全切换，不必再使用联合解决方案。 以前使用 ADFS 进行身份验证的应用现在可以仅使用 Azure AD。

![该关系图显示本地应用、业务线应用、SaaS 应用，并且显示了通过 Azure AD，Office 365 连接到 Active Directory 和 AD FS（以虚线表示）。 Active Directory 和 AD FS 不存在。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

你还可以将使用其他基于云的标识提供者的应用迁移到 Azure AD。 组织可能有多个标识访问管理 (IAM) 解决方案。 迁移到一个 Azure AD 基础结构是减少对 IAM 许可证（本地或云中）的依赖和基础结构成本的一个机会。 如果你可能已通过 M365 许可证支付了 Azure AD 的费用，则没有理由支付另一个 IAM 解决方案的额外费用。

## <a name="4-integrate-on-premises-applications"></a>4.集成本地应用程序

通常，应用程序只有在连接到企业网络时才允许访问，从而保持安全性。 但是，在越来越多的环境中，我们希望允许客户、合作伙伴和/或员工访问应用，而不考虑他们在世界各地的位置。 [Azure AD 应用程序代理](../app-proxy/what-is-application-proxy.md) (AppProxy) 是 Azure AD 的一项功能，它将现有的本地应用程序连接到 Azure AD，无需维护边缘服务器或其他其他基础结构来执行此操作。

![关系图显示应用程序代理服务的运行情况。 用户访问“https://sales.contoso.com”，并且他们的请求通过 Azure Active Directory 中的“https://sales-contoso.msappproxy.net”重定向到本地地址“http://sales”](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

你可以使用[教程：在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](../app-proxy/application-proxy-add-on-premises-application.md)，以启用应用程序代理并将本地应用程序添加到 Azure AD 租户。

此外，你可以集成应用程序传送控制器（如 F5 Big-IP APM 或 Zscaler Private Access）。 通过将这些功能与 Azure AD 集成，你可以与合作伙伴产品的流量管理和安全功能 Azure AD 进行新式身份验证和标识管理。 我们将这种解决方案称为[安全的混合访问](../manage-apps/secure-hybrid-access.md)。 如果你现在使用以下任何服务，我们将提供一些教程，指导你如何将它们与 Azure AD 集成。

- [Akamai 企业应用程序访问 (EAA)](../saas-apps/akamai-tutorial.md)
- [Citrix Application Deliver Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md)（以前称为 Citrix Netscaler）
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5.集成开发人员构建的应用

对于在公司内部构建的应用程序，开发人员可以使用 [Microsoft 标识平台](../develop/index.yml)来实现身份验证和授权。 与平台集成的应用程序将在 [Azure AD 中注册](../develop/quickstart-register-app.md)并进行管理，就像产品组合中的任何其他应用程序一样。

开发人员可以将平台用于内部使用的应用和面向客户的应用，使用平台还有其他优势。 [Microsoft 身份验证库 (MSAL)](../develop/msal-overview.md)，它是平台的一部分，使开发人员能够实现多种新式体验，例如多重身份验证和使用安全密钥来访问其应用，而无需自行实现。 此外，与 Microsoft 标识平台集成的应用可以访问 [Microsoft Graph](../develop/microsoft-graph-intro.md)，这是一种统一的 API 终结点，提供描述组织中的工作效率、标识和安全模式的 Microsoft 365 数据。 开发人员可以使用这些信息来实现提高用户工作效率的功能。 例如，通过识别用户最近与最近交互的人员并在应用的 UI 中将其呈现出来。

我们提供了[一系列视频](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX)，其中全面介绍了平台，并提供了支持的语言和平台的[许多代码示例](../develop/sample-v2-code.md)。

## <a name="next-steps"></a>后续步骤

- [用于将应用程序迁移到 Azure Active Directory 的资源](../manage-apps/migration-resources.md)