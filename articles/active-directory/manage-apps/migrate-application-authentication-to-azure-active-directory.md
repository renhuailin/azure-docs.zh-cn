---
title: 将应用程序身份验证迁移到 Azure Active Directory
description: 本白皮书详细介绍了将应用程序身份验证迁移到 Azure AD 的规划和优点。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365283ebc45c0f85a56d27d4cc90807de9a88fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742879"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>将应用程序身份验证迁移到 Azure Active Directory

## <a name="about-this-paper"></a>关于本文

本白皮书详细介绍了将应用程序身份验证迁移到 Azure AD 的规划和优点。 它专为 Azure 管理员和标识专业人员而设计。

本文将该过程分解为四个阶段，每个阶段都有详细的规划和退出条件，旨在帮助你规划迁移策略并了解 Azure AD 身份验证如何支持你的组织目标。

## <a name="introduction"></a>简介

如今，你的组织需要大量的应用程序（应用）才能使用户完成工作。 你可能每天都会继续添加、开发或停用应用。 用户从各种公司和个人设备以及范围广泛的位置访问这些应用程序。 他们通过多种方式打开应用，包括：

- 通过公司主页或门户

- 通过在其浏览器上添加书签

- 通过供应商的服务型软件 (SaaS) 应用的 URL

- 通过移动设备/应用程序管理 (MDM/MAM) 解决方案直接推送到用户的桌面或移动设备的链接

应用程序可能使用以下类型的身份验证：

- 本地联合身份验证解决方案，例如 Active Directory 联合身份验证服务 (ADFS) 和 Ping

- Active Directory，例如 Kerberos 身份验证和 Windows 集成身份验证

- 其他基于云的标识和访问管理 (IAM) 解决方案，例如 Okta 或 Oracle

- 本地 Web 基础结构，例如 IIS 和 Apache

- 云托管的基础结构，例如 Azure 和 AWS

为确保用户能够轻松安全地访问应用程序，你的目标是在本地环境和云环境中拥有一组访问控制和策略。

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) 提供一个通用标识平台，为你的用户、合作伙伴和客户提供单一标识，用于访问他们所需的应用程序并从任何平台和设备进行协作。

![Azure Active Directory 连接的关系图](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD 具有[一整套标识管理功能](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)。 通过将应用身份验证和授权标准化到 Azure AD，可获得这些功能所提供的益处。

有关更多迁移资源，请参阅：[https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>将应用身份验证迁移到 Azure AD 的益处

将应用身份验证移至 Azure AD 有助于管理风险和成本、提高工作效率并满足合规性和监管要求。

### <a name="manage-risk"></a>管理风险

若要保护你的应用，必须全面了解所有的风险因素。 将应用迁移到 Azure AD 可巩固安全解决方案。 通过这样做，你可以：

- 使用[条件访问策略](../conditional-access/overview.md)、[多重身份验证](../authentication/concept-mfa-howitworks.md)和基于风险的实时[标识保护](../identity-protection/overview-identity-protection.md)技术，提高用户访问应用程序和相关公司数据的安全性。

- 使用[实时](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)管理员访问权限，保护特权用户对你的环境的访问。

- 使用 [Azure AD 的多租户、地理分布、高可用性设计](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)，满足你最关键的业务需求。

- 使用你可能已部署的一个[安全混合访问合作伙伴集成](https://aka.ms/secure-hybrid-access)来保护旧版应用程序。

### <a name="manage-cost"></a>管理成本

组织可能有多个标识访问管理 (IAM) 解决方案。 迁移到一个 Azure AD 基础结构是减少对 IAM 许可证（本地或云中）的依赖和基础结构成本的一个机会。 如果你可能已通过 Microsoft 365 许可证支付 Azure AD 费用，则无需再额外支付其他 IAM 解决方案的费用。

通过 Azure AD，可通过以下方式降低基础结构成本：

- 使用 [Azure AD 应用程序代理](../app-proxy/application-proxy.md)提供对本地应用的安全远程访问。

- 通过[将 Azure AD 设置为可信通用标识提供者](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)，将应用程序与租户中的本地凭据方法分离。

### <a name="increase-productivity"></a>提高工作效率

经济和安全方面的益处促使组织采用 Azure AD，但如果用户也可从中受益，则实现完全的采用和遵从的可能性就更大。 使用 Azure AD，可以：

- 通过从任何设备和任何位置无缝安全地访问任何应用程序，改进最终用户的[单一登录 (SSO)](./what-is-single-sign-on.md) 体验。

- 利用自助式 IAM 功能，如[自助式密码重置](../authentication/concept-sspr-howitworks.md)和[自助式组合管理](../enterprise-users/groups-self-service-management.md)。

- 通过跨云环境和本地环境仅为每个用户管理单个标识，减少管理开销：

  - 根据 Azure AD 标识[自动预配](../app-provisioning/user-provisioning.md)用户帐户（在 [Azure AD 库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中）
  - 从 [Azure 门户](https://portal.azure.com/)中的 MyApps 面板访问所有应用

- 通过结合使用 [Microsoft 标识平台](../develop/v2-overview.md)和 Microsoft 身份验证库 (MSAL)，使开发人员能够保护对应用的访问并改进最终用户体验。

- 使用 [Azure AD B2B 协作](../external-identities/what-is-b2b.md)，使你的合作伙伴能够访问云资源。 使用云资源消除通过合作伙伴配置点到点联合身份验证的开销。

### <a name="address-compliance-and-governance"></a>解决合规性和监管问题

使用集成的审核工具和 API 来强制实施公司访问策略和监视用户对应用程序及相关数据的访问，从而确保符合法规要求。 借助 Azure AD，可通过利用[安全信息和事件管理 (SIEM) 工具](../reports-monitoring/plan-monitoring-and-reporting.md)的报表监视应用程序登录情况。 可通过门户或 API 访问这些报表，并以编程方式审核有权访问应用程序的人员，以及通过访问评审删除非活动用户的访问权限。

## <a name="plan-your-migration-phases-and-project-strategy"></a>规划迁移阶段和项目策略

技术项目的失败通常是由于预期不匹配、没有适当的利益干系人参与或者缺乏沟通造成的。 通过规划项目本身来确保你的成功。

### <a name="the-phases-of-migration"></a>迁移阶段

在使用这些工具之前，你应该了解如何对迁移过程进行透彻地思考。 通过几次直接面向客户的研讨会，我们推荐以下四个阶段：

![迁移阶段的示意图](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>组建项目团队

应用程序迁移是一项团队工作，你需要确保所有重要职位都有人担任。 高级业务主管的支持非常重要。 确保使一组合适的执行发起人、业务决策者和行业专家 (SME) 参与其中。

在迁移项目中，一个人可能扮演多个角色，或者多个人扮演一个角色，这取决于组织的规模和结构。 你可能还会依赖于其他在你的安全格局中扮演重要角色的团队。

下表包括关键角色及其贡献：

| 角色          | 参与                                              |
| ------------- | ---------------------------------------------------------- |
| 项目经理 | 负责指导项目的项目指导者，其责任包括：<br /> - 获取行政支持<br /> - 引入利益干系人<br /> - 管理计划、文档和通信 |
| 标识架构师/Azure AD 应用管理员 | 它们负责以下工作：<br /> - 与利益干系人合作设计解决方案<br /> - 将解决方案设计和操作过程编制成文档，以便移交给运营团队<br /> - 管理预生产环境和生产环境 |
| 本地 AD 运营团队 | 管理不同本地标识源（如 AD 林、LDAP 目录、HR 系统等）的组织。<br /> - 在同步之前执行所需的任何修正任务<br /> - 提供同步所需的服务帐户<br /> - 提供配置 Azure AD 联合身份验证的访问权限 |
| IT 支持经理 | IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。 |
| 安全所有者  | 安全团队的代表，可确保该计划符合组织的安全要求。 |
| 应用程序技术所有者 | 包括将与 Azure AD 集成的应用和服务的技术所有者。 他们提供了应包含在同步过程中的应用程序的标识特性。 他们通常与 CSV 代表有关系。 |
| 应用程序业务所有者 | 具有代表性的同事，他们可以从用户的角度提供关于用户体验和这种更改的实用性的信息，并负责应用程序的整体业务方面工作，这可能包括管理访问。 |
| 试点用户组 | 将测试作为其日常工作（试点体验）的一部分的用户，并提供反馈来指导其余的部署。 |

### <a name="plan-communications"></a>规划沟通

有效的业务参与和交流是取得成功的关键。 为利益干系人和最终用户提供用于获取信息并及时了解计划更新的途径，这点非常重要。 让每个人都了解迁移的价值、预期的时间线以及对任何临时业务中断进行规划的方式。 使用多个途径，如简报会话、电子邮件、一对一会议、横幅和市民集会所。

根据你为应用选择的通信策略，你可能希望提醒用户即将到来的停机时间。 你还应该验证最近没有需要推迟部署的变更或业务影响。

在下表中，可找到使利益干系人保持知情的最低限度的建议通信：

规划阶段和项目策略：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| 项目的认知和业务/技术价值 | 除最终用户外的所有人员 |
| 试点应用征集 | - 应用业务所有者<br />- 应用技术所有者<br />- 架构师和标识团队 |

阶段 1 - 发现并界定范围：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| - 应用程序信息征集<br />- 范围界定实践的结果 | - 应用技术所有者<br />- 应用业务所有者 |

阶段 2 - 对应用分类并规划试点：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| - 分类的结果及其对迁移计划的意义<br />- 初步迁移计划 | - 应用技术所有者<br /> - 应用业务所有者 |

阶段 3 - 规划迁移和测试：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| - 应用程序迁移测试的结果 | - 应用技术所有者<br />- 应用业务所有者 |
| - 通知（迁移即将到来）以及说明（迁移的最终用户体验）。<br />- 即将到来的停机时间，以及完整的通信（包括他们现在应该执行的操作、反馈以及获取帮助的方式） | - 最终用户（以及所有其他人员） |

阶段 4 - 管理并获取见解：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| 可用分析以及访问方式 | - 应用技术所有者<br />- 应用业务所有者 |

### <a name="migration-states-communication-dashboard"></a>迁移状态通信仪表板

传达迁移项目的整体状态至关重要，因为它显示了进度，并可帮助应用要进行迁移的应用所有者做好迁移的准备。 可使用 Power BI 或其他报告工具构建一个简单的仪表板，以在迁移期间提供对应用程序状态的可见性。

可考虑使用的迁移状态如下所示：

| 迁移状态       | 操作计划                                   |
| ---------------------- | --------------------------------------------- |
| 初始请求 | 查找应用并联系所有者以获取详细信息 |
| 评估完成 | 应用所有者评估应用要求并返回应用问卷</td>
| 正在进行配置 | 列出管理针对 Azure AD 的身份验证所需的更改 |
| 测试配置成功 | 评估更改，并在测试环境中针对测试 Azure AD 租户对应用进行身份验证 |
| 生产配置成功 | 更改配置，使其适用于生产 AD 租户，并在测试环境中评估应用身份验证 |
| 完成/注销 | 将应用的更改部署到生产环境，并针对生产 Azure AD 租户执行 |

这可确保应用所有者知道应用迁移和测试计划的内容，应用进行迁移的时间，以及已迁移的其他应用的结果。 你还可以考虑提供指向 bug 跟踪器数据库的链接，使所有者能够归档和查看要迁移的应用的问题。

### <a name="best-practices"></a>最佳实践

下面是我们的客户和合作伙伴的成功案例以及建议的最佳做法：

- [改进向 Azure Active Directory 迁移的过程的五个技巧](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364)，由 Patriot Consulting 公司提供，该公司是我们合作伙伴网络中的一员，致力于帮助客户安全地部署 Microsoft 云解决方案。

- [为你的 Azure AD 应用程序迁移制定风险管理策略](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488)，由 Edgile 公司提供，该公司是一家专注于 IAM 和风险管理解决方案的合作伙伴。

## <a name="phase-1-discover-and-scope-apps"></a>阶段 1：发现应用并界定应用范围

应用程序发现和分析是一种可为你提供良好开端的基础实践。 你可能并非所有内容都了解，因此要准备好适应未知的应用。

### <a name="find-your-apps"></a>查找应用

应用程序迁移的第一个决策点是决定要迁移哪些应用，应保留哪些应用（如果有），以及要弃用哪些应用。 你始终有机会弃用你将不会在组织中使用的应用。 可通过多种方法在组织中查找应用。 发现应用时，请确保包括正在开发的应用和计划的应用。在以后的所有应用中，请使用 Azure AD 进行身份验证。

使用 Active Directory 联合身份验证服务 (AD FS) 收集正确的应用清单：

- **使用 Azure AD Connect Health。** 如果你有 Azure AD Premium 许可证，建议部署 [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) 来分析本地环境中的应用使用情况。 可使用 [ADFS 应用程序报表](./migrate-adfs-application-activity.md)（预览），发现可迁移的 ADFS 应用程序并评估要迁移的应用程序的就绪情况。 完成迁移后，部署 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)，它使你能够在云中持续监视组织中的影子 IT。

- AD FS 日志分析。 如果你没有 Azure AD Premium 许可证，建议使用基于 [PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) 的 ADFS 到 Azure AD 应用迁移工具。 请参阅[解决方案指南](./migrate-adfs-apps-to-azure.md)：

[将应用从 Active Directory 联合身份验证服务 (AD FS) 迁移到 Azure AD。](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>使用其他标识提供者 (IdP)

对于其他标识提供者（例如 Okta 或 Ping），可使用其各自的工具导出应用程序清单。 可考虑查看在 Active Directory 上注册的、与组织中的 Web 应用相对应的服务原则。

### <a name="using-cloud-discovery-tools"></a>使用 Cloud Discovery 工具

在云环境中，需要丰富的显示效果、对数据传输的控制和成熟分析服务，用于跨所有云服务发现和防范网络威胁。 可使用以下工具收集云应用清单：

- 云访问安全代理 (CASB) - [CASB](/cloud-app-security/) 通常与防火墙一起工作以提供对员工云应用程序使用情况的可见性，且它有助于保护你的公司数据免受网络安全威胁的影响。 CASB 报表可帮助你确定组织中最常使用的应用，以及迁移到 Azure AD 的早期目标。

- Cloud Discovery - 通过配置 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)，可了解云应用的使用情况，并且可发现未批准的应用或影子 IT 应用。

- API - 对于连接到云基础结构的应用，可使用这些系统上的 API 和工具开始收集托管应用清单。 在 Azure 环境中：

  - 使用 [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) cmdlet 获取有关 Azure 网站的信息。

  - 使用 [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) cmdlet 获取有关 Azure Web 应用的信息。
D
  - 可使用 [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools) 在 Windows 命令行中查找在 Microsoft IIS 上运行的所有应用。

  - 使用[应用程序](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)和[服务主体](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)，获取有关 Azure AD 中某目录的应用和应用实例的信息。

### <a name="using-manual-processes"></a>使用手动过程

采用上述自动化方法后，你就可以很好地处理你的应用程序。 但是，你可能会考虑执行以下操作，以确保能够很好地覆盖所有用户访问区域：

- 联系组织中的各个业务所有者，查找组织中正在使用的应用程序。

- 在代理服务器上运行 HTTP 检查工具或分析代理日志，查看流量通常被路由到的位置。

- 查看热门公司门户站点的网络日志，了解用户访问最多的链接。

- 与管理人员或其他关键业务成员联系，确保你已涵盖业务关键应用。

### <a name="type-of-apps-to-migrate"></a>要迁移的应用类型

找到应用后，在组织中标识这些类型的应用：

- 已使用新式身份验证协议的应用

- 使用你选择进行现代化的旧版身份验证协议的应用

- 使用你选择不进行现代化的旧版身份验证协议的应用

- 新的业务线 (LoB) 应用

### <a name="apps-that-use-modern-authentication-already"></a>已使用新式身份验证的应用

已现代化的应用最有可能被移动到 Azure AD。 这些应用已使用新式身份验证协议（如 SAML 或 OpenID Connect），并可重新进行配置以向 Azure AD 进行身份验证。

除了 [Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中的选项之外，这些应用还可以是组织中已经存在的应用，或者来自不属于 Azure AD 库的供应商的任何第三方应用（[非库应用程序](./add-application-portal.md)）。

选择进行现代化的旧版应用

对于想要进行现代化的旧版应用，通过移动到 Azure AD 进行核心身份验证和授权，可解锁 [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) 和 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) 提供的所有功能和数据丰富性。

建议将这些应用程序的身份验证堆栈代码从旧版协议（如 Windows 集成身份验证、Kerberos 约束委派、基于 HTTP 头的身份验证）更新为新式协议（如 SAML 或 OpenID Connect）。

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>选择不进行现代化的旧版应用

对于某些使用旧版身份验证协议的应用，出于业务原因，有时使其身份验证现代化不是正确的做法。 这包括下列类型的应用：

- 出于合规性或控制原因而保存在本地的应用。

- 已连接到你不想更改的本地标识或联合身份验证提供者的应用。

- 使用你没有计划迁移的本地身份验证标准开发的应用

Azure AD 可为这些旧版应用带来极大的好处，因为你可以在完全不接触这些应用的情况下，针对这些应用启用新式 Azure AD 安全和监管功能，如[多重身份验证](../authentication/concept-mfa-howitworks.md)、[条件访问](../conditional-access/overview.md)、[标识保护](../identity-protection/index.yml)、[委派的应用程序访问](./access-panel-manage-self-service-access.md)以及[访问评审](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review)！

首先，可使用简单的身份验证方法（如密码保管），通过 Azure AD [应用程序代理](../app-proxy/application-proxy-configure-single-sign-on-password-vaulting.md)将这些应用扩展到云中，使用户快速地进行迁移，或者可使用你可能已部署的应用程序交付控制器通过我们的[合作伙伴集成](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/)来实现这一点。

### <a name="new-line-of-business-lob-apps"></a>新的业务线 (LoB) 应用

你通常会开发 LoB 应用供组织内部使用。 如果你在管道中有新的应用，则建议使用 [Microsoft 标识平台](../develop/v2-overview.md)来实现 OpenID Connect。

### <a name="apps-to-deprecate"></a>要弃用的应用

没有明确的所有者以及明确的维护和监视的应用会给组织带来安全风险。 在以下情况下，请考虑弃用应用程序：

- 应用的功能对于其他系统来说高度冗余 • 没有业务所有者 

- 应用显然没有使用。

建议不要弃用可产生重大影响的业务关键应用程序。 在这些情况下，请与业务所有者一起确定正确的策略。

### <a name="exit-criteria"></a>退出条件

若你实现了以下操作，则你在此阶段获得了成功：

- 对迁移范围内的系统（可在迁移到 Azure AD 后停用）有很好的了解

- 应用列表，其中包括：

  - 这些应用连接到的系统
  - 用户访问它们的位置和设备
  - 它们是否将被迁移、弃用或与 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) 连接。

> [!NOTE]
> 可下载[应用程序发现工作表](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)，记录要迁移到 Azure AD 身份验证的应用程序，以及要保留但使用 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) 管理的应用程序。

## <a name="phase-2-classify-apps-and-plan-pilot"></a>阶段 2：对应用分类并规划试点

对应用的迁移进行分类是一项重要的实践。 并非每个应用都需要同时进行迁移和转换。 收集每个应用的相关信息后，可合理安排哪些应用应该首先进行迁移，哪些应用可能需要更多的时间。

### <a name="classify-in-scope-apps"></a>对范围内的应用进行分类

对于这种情况，一种思考方式是沿着业务关键性、使用情况和生命周期的轴线进行思考，每个轴线都依赖于多个因素。

### <a name="business-criticality"></a>业务关键性

业务关键性对于每个业务都有不同的维度，但是你应该考虑的两个度量值是“特性和功能”以及“用户配置文件” 。 为具有独特功能的应用分配一个比具有冗余或过时功能的应用更高的分值。

![“特性和功能”和“用户配置文件”的频谱图](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>使用情况

使用率高的应用程序应该比使用率低的应用程序获得更大的值。 为外部用户、执行用户或安全团队用户的应用分配更大的值。 对于迁移组合中的每个应用，请完成这些评估。

![“用户量”和“用户广度”的频谱图](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

确定了业务关键性和使用情况的值后，你就可以确定应用程序生命周期，并创建一个优先级矩阵。 请参阅下面的一个矩阵：

![显示使用情况、预期生命周期和业务关键性之间关系的三角图](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>确定要迁移的应用的优先级

可根据组织的需求，选择从优先级最低的应用或优先级最高的应用开始应用迁移。

如果你可能没有使用 Azure AD 和标识服务的经验，请考虑首先将优先级最低的应用移动到 Azure AD。 这将最大程度地减少业务影响，并且你可以积聚继续操作的动力。 成功移动了这些应用并获得了利益干系人的信任后，可继续迁移其他应用。

如果没有明确的优先级，你应考虑先移动 [Azure AD 库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中的应用，并支持多个标识提供者（ADFS 或 Okta），因为它们更易于集成。 这些应用很可能是组织中优先级最高的应用。 为了帮助将 SaaS 应用程序与 Azure AD 集成，我们制作了一系列[教程](../saas-apps/tutorial-list.md)用于演示配置。

当应用迁移存在截止日期时，这些优先级最高的应用桶将承担主要工作负载。 你最终可选择优先级较低的应用，因为即使你已经更改了截止日期，它们也不会改变成本。 即使你必须续订许可证，也只需要少量费用。

除了此分类之外，根据迁移的紧迫性，你还可以考虑制定一个迁移计划，在此计划内，应用所有者必须参与迁移他们的应用。 在此过程结束时，你应该有一个列表，其中列出了按优先级排列的桶中所有要迁移的应用程序。

### <a name="document-your-apps"></a>记录应用

首先，从收集应用程序的关键详细信息开始。 [应用程序发现工作表](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)可帮助你快速做出迁移决策，并立即向业务部门提出建议。

对制定迁移决策非常重要的信息包括：

- 应用名称 - 对于该业务，此应用的名称是什么？

- 应用类型 - 是否为第三方 SaaS 应用？ 自定义业务线 Web 应用？ 还是 API？

- 业务关键性 - 它的关键性高吗？ 低？ 还是介于两者之间？

- 用户访问量 - 是否每个人都能访问此应用，还是只是少数人员能访问？

- 计划的生命周期 - 此应用会持续存在多长时间？ 不到六个月？ 超过两年？

- 当前标识提供者 - 此应用的主要 IdP 是什么？ 或者它是否依赖于本地存储？

- 身份验证方法 - 应用是否使用开放标准进行身份验证？

- 是否计划更新应用代码 - 应用是处于计划开发中还是正在积极开发中？

- 是否计划将应用保留在本地 - 是否要在数据中心长期保留应用？

- 应用是否依赖于其他应用或 API - 应用当前是否调用其他应用或 API？

- 应用是否在 Azure AD 库中 - 应用当前是否已与 [Azure AD 库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)集成？

稍后将对你有所帮助，但你不需要立即做出迁移决策的其他数据包括：

- 应用 URL - 用户转到何处访问应用？

- 应用说明 - 应用功能的简要说明是什么？

- 应用所有者 - 在该业务中，谁是应用的主要 POC？

- 常规注释或备注 - 有关应用或业务所有权的任何其他常规信息

对应用程序进行了分类并记录了详细信息后，请确保要使业务所有者认可你的计划迁移策略。

### <a name="plan-a-pilot"></a>规划试点

为试点选择的应用应该代表你的组织的关键标识和安全要求，并且你必须得到应用程序所有者的明确认可。 试点通常在单独的测试环境中运行。 请参阅部署计划页上的[试点最佳做法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

请勿忘了你的外部合作伙伴。 确保他们参与到迁移计划和测试中。 最后，确保在出现中断问题时，他们可以联系你的支持人员。

### <a name="plan-for-limitations"></a>对限制进行规划

虽然有些应用可以轻松迁移，但由于多个服务器或实例，其他应用可能需要更长时间。 例如，由于自定义登录页，SharePoint 迁移可能需要更长时间。

许多 SaaS 应用程序供应商对更改 SSO 连接收费。 请与他们核实并为此做好规划。

Azure AD 还有一些你应注意的[服务限制和限制](../enterprise-users/directory-service-limits-restrictions.md)。

### <a name="app-owner-sign-off"></a>应用所有者签名

使用广泛的业务关键应用程序可能需要一组试点用户在试点阶段测试该应用。 在预生产环境或试点环境中测试应用后，请确保应用业务所有者在将应用和所有用户迁移到生产用 Azure AD 进行身份验证之前进行了签名，对操作表示认可。

### <a name="plan-the-security-posture"></a>规划安全状况

在开始迁移过程之前，请花时间充分考虑你希望为企业标识系统提供的安全状况。 这需要收集以下宝贵的信息集：要访问你的数据的标识、设备和位置。

### <a name="identities-and-data"></a>标识和数据

大多数组织对标识和数据保护有特定的要求，这些要求因行业分部和组织内的工作职能而异。 有关建议（包括一组规定的[条件访问策略](../conditional-access/overview.md)和相关功能），请参阅[标识和设备访问配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)。

可使用此信息来保护对与 Azure AD 集成的所有服务的访问。 这些建议与 Microsoft 安全功能分数和 [Azure AD 中的标识分数](../fundamentals/identity-secure-score.md)相一致。 评分有助于：

- 客观衡量标识安全状况

- 规划标识安全改进

- 审查改进措施的成败

这还将帮助你实施[保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)。 使用本指南作为组织的起点，调整策略以满足组织的特定要求。

### <a name="who-is-accessing-your-data"></a>谁在访问你的数据？

Azure AD 支持的应用和资源主要有两类用户：

- 内部：在标识提供者内拥有帐户的员工、承包商和供应商。 相对于其他员工，对于经理或领导，这可能需要使用不同的规则进行进一步的调整。

- 外部：通过 [Azure AD B2B 协作](../external-identities/what-is-b2b.md)，在正常业务过程中与你的组织交互的销售商、供应商、分销商或其他业务合作伙伴。

你可以为这些用户定义组，并以不同方式填充这些组。 可选择管理员必须手动将成员添加到组中，或者可启用自助服务组成员身份。 可使用[动态组](../enterprise-users/groups-dynamic-membership.md)建立规则，根据指定的条件自动将成员添加到组中。

外部用户也可以引用客户。 [Azure AD B2C](../../active-directory-b2c/overview.md)（一个独立的产品）支持客户身份验证。 但是，它不在本文的介绍范围之内。

### <a name="devicelocation-used-to-access-data"></a>用于访问数据的设备/位置

用户用于访问应用的设备和位置也很重要。 物理连接到公司网络的设备更安全。 通过 VPN 来自网络外部的连接可能需要仔细检查。

![显示“用户位置”与“数据访问”之间的关系的图](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

考虑到资源、用户和设备的这些方面，可选择使用 [Azure AD 条件访问](../conditional-access/overview.md)功能。 条件访问超出了用户权限：它基于多种因素的组合，例如用户或组的标识、用户所连接到的网络、所使用的设备和应用程序以及尝试访问的数据类型。 授予用户的访问权限可适应这一组范围更广的条件。

### <a name="exit-criteria"></a>退出条件

若你完成了以下操作，则你在此阶段获得了成功：

- 了解你的应用
  - 完整记录了要迁移的应用
  - 根据业务关键性、使用量和生命周期对应用进行了优先排序

- 选择了代表试点要求的应用

- 业务所有者认同你的优先排序和策略

- 了解你的安全状况需求以及实现它们的方式

## <a name="phase-3-plan-migration-and-testing"></a>阶段 3：规划迁移和测试

获得业务认可后，下一步就是开始将这些应用迁移到 Azure AD 身份验证。

### <a name="migration-tools-and-guidance"></a>迁移工具和指南

使用下面的工具和指南，按照将应用程序迁移到 Azure AD 所需的具体步骤进行操作：

- 常规迁移指南 - 使用 [Azure AD 应用迁移工具包](./migration-resources.md)中的白皮书、工具、电子邮件模板和应用程序问卷来发现应用、对应用分类并迁移应用。

- SaaS 应用程序 - 请参阅[数百个 SaaS 应用程序教程](../saas-apps/tutorial-list.md)列表和完整的 [Azure AD SSO 部署计划](https://aka.ms/ssodeploymentplan)，逐步完成端到端过程。

- 本地运行的应用程序 - 了解有关 [Azure AD 应用程序代理](../app-proxy/application-proxy.md)的所有信息，并使用完整的 [Azure AD 应用程序代理部署计划](https://aka.ms/AppProxyDPDownload)快速开始操作。

- 正在开发的应用 - 请参阅分步[集成](../develop/quickstart-register-app.md)和[注册](../develop/quickstart-register-app.md)指南。

迁移后，可选择向用户发送成功部署的通知，并提醒他们需要采取的任何新步骤。

### <a name="plan-testing"></a>规划测试

在迁移过程中，应用可能已具有在常规部署过程中使用的测试环境。 可继续使用此环境进行迁移测试。 如果测试环境当前不可用，则可使用 Azure 应用服务或 Azure 虚拟机来设置一个测试环境，具体取决于应用程序的体系结构。 可选择设置一个独立的测试 Azure AD 租户，以在开发应用配置时使用。 此租户将以干净状态启动，并且不会配置为与任何系统同步。

可通过使用测试用户登录来测试每个应用，并确保所有功能与迁移之前的功能相同。 如果你在测试期间确定用户需要更新他们的 [MFA](../authentication/howto-mfa-userstates.md) 或 [SSPR](../authentication/tutorial-enable-sspr.md) 设置，或者你要在迁移期间添加此功能，请务必将其添加到最终用户通信计划中。 请参阅 [MFA](https://aka.ms/mfatemplates) 和 [SSPR](https://aka.ms/ssprtemplates) 最终用户通信模板。

迁移应用后，转到 [Azure 门户](https://aad.portal.azure.com/)，测试迁移是否成功。 请根据以下说明进行操作：

- 选择“企业应用程序”&gt;“所有应用程序”，并从列表中找到你的应用。

- 选择“管理”&gt;“用户和组”，将至少一个用户或组分配到应用。

- 选择“管理”&gt;“条件访问”。 查看策略列表，确保没有使用[条件访问策略](../conditional-access/overview.md)阻止对应用程序的访问。

根据你配置应用的方式，验证 SSO 是否正常工作。

| 身份验证类型      | 正在测试                                             |
| ------------------------ | --------------------------------------------------- |
| OAuth/OpenID Connect | 选择“企业应用程序”&gt;“权限”，并确保你已在应用的用户设置中同意该应用程序在你的组织中使用。 |
| **基于 SAML 的 SSO** | 使用“单一登录”下的[测试 SAML 设置](./debug-saml-sso-issues.md)按钮。 |
| 基于密码的 SSO | 下载并安装 [MyApps 安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。 此扩展可帮助你启动组织中任何需要你使用 SSO 过程的云应用。 |

| [应用程序代理](../app-proxy/application-proxy.md) | 确保连接器正在运行并已分配给应用程序。 访问[应用程序代理故障排除指南](../app-proxy/application-proxy-troubleshoot.md)以获取进一步的帮助。 |

### <a name="troubleshoot"></a>疑难解答

如果遇到问题，请查看[应用故障排除指南](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md)以获取帮助。 此外，还可以查看故障排除文章，请参阅[登录到基于 SAML 的单一登录配置的应用时出现问题](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)。

### <a name="plan-rollback"></a>规划回滚

如果迁移失败，最佳策略是回滚并测试。 可执行以下步骤来缓解迁移问题：

- 获取应用的现有配置的屏幕截图。 如果必须再次重新配置应用，你可以返回查看该截图。

- 如果云身份验证出现问题，你还可以考虑提供指向旧版身份验证的链接。

- 在完成迁移之前，请勿通过早期的标识提供者更改现有配置。

- 首先，迁移支持多个 IdP 的应用。 如果出现问题，你始终可更改为首选的 IdP 配置。

- 确保你的应用体验有一个“反馈”按钮或指向你的支持人员的指针，以解决问题 。

### <a name="exit-criteria"></a>退出条件

若你已完成以下操作，则你在此阶段获得了成功：

- 确定每个应用的迁移方式

- 查看迁移工具

- 规划测试，包括测试环境和组

- 规划回滚

## <a name="phase-4-plan-management-and-insights"></a>阶段 4：规划管理和见解

迁移应用后，必须确保：

- 用户可安全地访问和管理

- 你可获得关于使用情况和应用运行状况的适当见解

建议根据组织的需要采取以下措施。

### <a name="manage-your-users-app-access"></a>管理用户的应用访问

迁移应用后，可通过多种方式丰富你的用户体验

使应用可被发现

将用户指向 [MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) 门户体验。 在此处，他们可以访问所有基于云的应用，使用 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)提供的应用，以及使用[应用程序代理](../app-proxy/application-proxy.md)的应用（前提是他们有权访问这些应用）。

可指导用户了解如何发现其应用：

- 使用[现有的单一登录](./view-applications-portal.md)功能，将用户链接到任何应用

- 启用对应用的[自助式应用程序访问](./manage-self-service-access.md)，让用户添加你策展的应用

- [对最终用户隐藏应用程序](./hide-application-from-user-portal.md)（默认 Microsoft 应用或其他应用），使他们确实需要的应用更易于被发现

### <a name="make-apps-accessible"></a>使应用可访问

允许用户从其移动设备访问应用。 用户可在其 [iOS](./hide-application-from-user-portal.md) 7.0/更高版本或 [Android](./hide-application-from-user-portal.md) 设备上，使用 Intune Managed Browser 访问 MyApps 门户。

用户可下载 Intune Managed Browser：

- 对于 Android 设备，可通过 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.intune)下载

- 对于 Apple 设备，可通过[Apple App Store](https://apps.apple.com/us/app/intune-company-portal/id719171358) 下载（或用户可下载[适用于 iOS 的 Apps 移动应用](https://appadvice.com/app/my-apps-azure-active-directory/824048653)）

允许用户通过浏览器扩展打开其应用。

用户可在 [Chrome](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)、[FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) 或 [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) 中[下载 MyApps 安全登录扩展](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)，并可以直接从浏览器栏启动应用以执行以下操作：

- 搜索其应用并显示最近使用的应用

- 自动将你在[应用程序代理](../app-proxy/application-proxy.md)中配置的内部 URL 转换为适当的外部 URL。 用户现可使用他们熟悉的链接（不管他们位于何处）。

允许用户从 Office.com 打开其应用。

用户可转到 [Office.com](https://www.office.com/)，搜索他们的应用，并在他们工作的位置显示最近使用的应用程序。

### <a name="secure-app-access"></a>保护应用访问

Azure AD 提供了一个集中的访问位置来管理已迁移的应用。 转到 [Azure 门户](https://portal.azure.com/)，并启用以下功能：

- 保护用户对应用的访问。 启用[条件访问策略](../conditional-access/overview.md)或[标识保护](../identity-protection/overview-identity-protection.md)，以根据设备状态、位置等来保护用户对应用程序的访问。

- 自动预配。 对于用户需要访问的各种第三方 SaaS 应用序，设置[自动预配用户](../app-provisioning/user-provisioning.md)。 除了创建用户标识外，这还包括在状态或角色发生更改时维护和删除用户标识。

- 委托用户访问管理 。 根据需要，启用对应用的自助式应用程序访问，并分配一个业务审批者来批准对这些应用的访问。 对分配给应用集合的组使用[自助服务组管理](../enterprise-users/groups-self-service-management.md)。

- 委派管理员访问权限。 使用目录角色向用户分配管理员角色（例如应用程序管理员、云应用程序管理员或应用程序开发人员）。

### <a name="audit-and-gain-insights-of-your-apps"></a>审核应用并获得应用的见解

你还可使用 [Azure 门户](https://portal.azure.com/)在一个集中的位置审核所有应用：

- 使用“企业应用程序 Audit”审核应用，或从 [Azure AD 报告 API](../reports-monitoring/concept-reporting-api.md) 访问相同的信息以集成到你最喜爱的工具中 。

- 对于使用 OAuth/OpenID Connect 的应用，使用企业应用程序 Permissions 查看应用的权限 。

- 使用企业应用程序 Sign-Ins 获得登录见解 。从 [Azure AD 报告 API](../reports-monitoring/concept-reporting-api.md) 访问相同的信息

- 通过 [Azure AD PowerBI 内容包](../reports-monitoring/howto-use-azure-monitor-workbooks.md)直观显示应用的使用情况

### <a name="exit-criteria"></a>退出条件

若你完成了以下操作，则你在此阶段获得了成功：

- 为用户提供安全的应用访问

- 管理以审核已迁移应用并获取已迁移应用的见解

### <a name="do-even-more-with-deployment-plans"></a>通过部署计划实现更多操作

部署计划可引导逐步了解 Azure AD 解决方案的业务价值、规划、实施步骤和管理（包括应用程序迁移场景）。 部署计划汇集了你开始部署和获取 Azure AD 功能的价值所需的所有内容。 部署指南包括 Microsoft 推荐的最佳做法、最终用户通信、计划指南、实施步骤、测试用例等内容。

许多[部署计划](../fundamentals/active-directory-deployment-plans.md)都可供你使用，并且我们会不断提供更多部署计划！

### <a name="contact-support"></a>联系支持人员

访问以下支持链接，创建或跟踪支持票证并监视运行状况。

- Azure 支持：可致电 [Microsoft 支持部门](https://azure.microsoft.com/support)，根据你与 Microsoft 的企业协议，

针对任何 Azure 标识部署问题提交票证。

- FastTrack：如果你已购买企业移动性和安全性 (EMS) 或 Azure AD Premium 许可证，则你有资格从 [FastTrack 计划](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)获取部署帮助。

- 与产品工程团队合作：如果你要处理一个包含数百万用户的主要客户部署，则你有权获得 Microsoft 帐户团队或云解决方案架构师的支持。 根据项目的部署复杂性，可直接与 [Azure标识产品工程团队合作](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)。

- Azure AD 标识博客：订阅 [Azure AD 标识博客](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity)，随时了解由标识工程团队直接提供的所有最新产品公告、深度信息和路线图信息。
