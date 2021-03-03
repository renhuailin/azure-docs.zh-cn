---
title: 将应用程序身份验证迁移到 Azure Active Directory
description: 本白皮书详细介绍了将应用程序身份验证迁移到 Azure AD 的规划和优点。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1a0be0b7834632ba79af7dfe6c3a4fa25c0316
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645505"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>将应用程序身份验证迁移到 Azure Active Directory

## <a name="about-this-paper"></a>关于本文

本白皮书详细介绍了将应用程序身份验证迁移到 Azure AD 的规划和优点。 它适用于 Azure 管理员和标识专业人员。

将该过程分解为四个阶段，每个阶段都有详细的规划和退出条件，旨在帮助你规划迁移策略并了解 Azure AD authentication 如何支持你的组织目标。

## <a name="introduction"></a>简介

如今，你的组织需要许多 (应用程序的，以便用户) 完成工作。 你可能会继续每天添加、开发或停用应用。 用户从各种公司和个人设备以及位置访问这些应用程序。 它们通过多种方式打开应用，包括：

- 通过公司主页或门户

- 通过在其浏览器上书签书签

- 通过供应商的软件即服务 URL (SaaS) 应用

- 通过移动设备/应用程序管理 (MDM/MAM) 解决方案直接推送到用户的桌面或移动设备的链接

您的应用程序可能使用以下类型的身份验证：

- 本地联合身份验证解决方案 (例如 Active Directory 联合身份验证服务 (ADFS) 和 Ping) 

- Active Directory (例如 Kerberos 身份验证和 Windows 集成身份验证) 

- 其他基于云的标识和访问管理 (IAM) 解决方案 (如 Okta 或 Oracle) 

- 本地 web 基础结构 (如 IIS 和 Apache) 

- 云托管的基础结构 (例如 Azure 和 AWS) 

**为了确保用户能够轻松安全地访问应用程序，你的目标是在本地和云环境中拥有一组访问控制和策略。**

[Azure Active Directory (Azure AD) ](../fundamentals/active-directory-whatis.md) 提供一个通用标识平台，为你的用户、合作伙伴和客户提供单一标识，以访问他们所需的应用程序，并从任何平台和设备进行协作。

![Azure Active Directory 连接的关系图](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD 具有一整套 [标识管理功能](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)。 通过标准化应用程序身份验证和授权 Azure AD，可以获得这些功能所提供的好处。

请参阅中的其他迁移资源 [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>将应用身份验证迁移到 Azure AD 的好处

将应用程序身份验证转移到 Azure AD 有助于管理风险和成本，提高工作效率，并满足合规性和监管要求。

### <a name="manage-risk"></a>管理风险

保护你的应用程序需要你完全查看所有风险因素。 将应用迁移到 Azure AD 会合并安全解决方案。 利用它，您可以：

- 使用 [条件性访问策略](../conditional-access/overview.md)、 [多重身份验证](../authentication/concept-mfa-howitworks.md)和基于风险的实时 [Identity Protection](../identity-protection/overview-identity-protection.md) 技术，改进安全用户对应用程序和相关公司数据的访问。

- 使用实时管理员访问权限保护用户对您的 [环境的访问](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) 权限。

- 使用 [Azure AD 的多租户、地理分布、高可用性设计](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)满足最关键的业务需求。

- 使用我们已部署的 [安全混合访问伙伴集成](https://aka.ms/secure-hybrid-access) 之一保护你的旧应用程序。

### <a name="manage-cost"></a>管理成本

你的组织可能有多个 (IAM) 解决方案的标识访问管理。 迁移到一个 Azure AD 基础结构是为了减少 (本地或云) 和基础结构成本中的 IAM 许可证的依赖项。 如果你可能已通过 M365 许可证为 Azure AD 付费，则没有理由支付另一个 IAM 解决方案的额外成本。

**使用 Azure AD，可以通过以下方式降低基础结构成本：**

- 使用 [Azure AD 应用程序代理](./application-proxy.md)提供对本地应用程序的安全远程访问。

- 通过 [将 Azure AD 设置为受信任的通用标识提供程序](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)，将应用程序与本地凭据方法分离。

### <a name="increase-productivity"></a>提高工作效率

经济性和安全优势使组织能够采用 Azure AD，但如果用户也受益，则更有可能实现完全采用和符合性。 通过 Azure AD，您可以：

- 通过对任何设备和任何位置的任何应用程序进行无缝和安全的访问，提高最终用户的 [单一 Sign-On (SSO) ](./what-is-single-sign-on.md) 体验。

- 利用自助服务 IAM 功能，如 [自助服务密码重置](../authentication/concept-sspr-howitworks.md) 和 [SelfService 组管理](../enterprise-users/groups-self-service-management.md)。

- 通过在云环境和本地环境中仅管理每个用户的单个标识，减少管理开销：

  - 基于 Azure AD 标识，[自动预配](../app-provisioning/user-provisioning.md) [Azure AD 库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中的用户帐户 () 
  - 从[Azure 门户](https://portal.azure.com/)中的 MyApps 面板访问所有应用

- 使开发人员能够通过使用 microsoft [标识平台](../develop/v2-overview.md) 和 Microsoft 身份验证库（ (MSAL) ）来保护对应用程序的访问并改善最终用户体验。

- 使用 [AZURE AD B2B 协作](../external-identities/what-is-b2b.md)为你的合作伙伴提供对云资源的访问权限。 这消除了配置与合作伙伴的点到点联合的开销。

### <a name="address-compliance-and-governance"></a>满足合规性和监管

通过强制实施公司访问策略和监视用户对应用程序和关联数据的访问，使用集成的审核工具和 Api，确保符合法规要求。 使用 Azure AD，可以通过利用 [安全事件和事件监视 (SIEM) 工具](../reports-monitoring/plan-monitoring-and-reporting.md)的报表来监视应用程序登录。 你可以从门户或 Api 访问报表，并以编程方式审核谁有权访问你的应用程序，并通过访问评审删除对非活动用户的访问权限。

## <a name="plan-your-migration-phases-and-project-strategy"></a>规划迁移阶段和项目策略

当技术项目失败时，通常是由于预期不匹配、没有涉及到正确的利益干系人或缺少通信。 通过规划项目本身来确保成功。

### <a name="the-phases-of-migration"></a>迁移阶段

在了解这些工具之前，您应该了解如何考虑迁移过程。 通过多个直接客户研讨会，我们建议以下四个阶段：

![迁移阶段示意图](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>召集项目团队

应用程序迁移是团队的工作，您需要确保所有的重要位置都已填充。 高级业务主管的支持非常重要。 确保你涉及到正确的一组执行发起人、业务决策者和主题专家 (Sme。 ) 

在迁移项目过程中，一个人可以满足多个角色的需要，或多个用户可以满足每个角色，具体取决于您的组织的大小和结构。 您还可能依赖于在安全环境中扮演重要角色的其他团队。

下表包含关键角色及其发布内容：

| 角色          | 参与                                              |
| ------------- | ---------------------------------------------------------- |
| **项目经理** | 项目指导负责指导项目，其中包括：<br /> -获取支持人员支持<br /> -引入利益干系人<br /> -管理计划、文档和通信 |
| **标识架构师/Azure AD 应用管理员** | 它们负责以下操作：<br /> -设计与利益干系人合作的解决方案<br /> -记录用于移交给运营团队的解决方案设计和操作过程<br /> -管理预生产环境和生产环境 |
| **本地 AD 运营团队** | 管理不同本地标识源（如 AD 林、LDAP 目录、HR 系统等）的组织。<br /> -在同步之前执行所需的任何补救任务<br /> -提供同步所需的服务帐户<br /> -提供用于配置联合身份验证的访问 Azure AD |
| **IT 支持经理** | IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。 |
| **安全所有者**  | 安全团队的代表，它可以确保计划满足组织的安全要求。 |
| **应用程序技术所有者** | 包括将与 Azure AD 集成的应用和服务的技术所有者。 它们提供了应包含在同步过程中的应用程序的标识特性。 它们通常与 CSV 代表建立关系。 |
| **应用程序企业所有者** | 有代表性的同事，他们可以提供用户体验的输入和从用户的角度来了解此更改，并且拥有应用程序的总体业务方面，其中可能包括管理访问权限。 |
| **试验用户组** | 将测试为其日常工作的一部分的用户、试验体验，并提供反馈以指导部署的其余部分。 |

### <a name="plan-communications"></a>规划沟通

有效的业务参与和交流是取得成功的关键。 重要的是，使利益干系人和最终用户可以获得信息并及时了解计划更新。 让每个人都了解迁移的价值、预期的时间线，以及如何规划任何临时业务中断。 使用多个途径，如简报会话、电子邮件、一对一会议、横幅和 townhalls。

根据你为应用选择的通信策略，你可能希望提醒用户挂起的停机时间。 还应验证延迟部署所需的最新更改或业务影响。

在下表中，你将找到用于使利益干系人获得通知的最小建议通信：

**规划阶段和项目策略**：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| 项目的认知和业务/技术价值 | 除最终用户外的所有用户 |
| 试点应用的请求 | -应用企业所有者<br />-应用技术所有者<br />-架构师和标识团队 |

**阶段 1-发现和范围**：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| -应用程序信息的请求<br />-作用域练习的结果 | -应用技术所有者<br />-应用企业所有者 |

**阶段 2-分类应用和计划试验**：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| -分类结果和迁移计划的意思<br />-初步迁移计划 | -应用技术所有者<br /> -应用企业所有者 |

**阶段 3-规划迁移和测试**：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| -应用程序迁移测试的结果 | -应用技术所有者<br />-应用企业所有者 |
| -通知迁移即将推出，并解释最终用户体验。<br />-停机并完成通信，包括他们现在应执行的操作、反馈以及如何获得帮助 | -最终用户 (和所有其他用户)  |

**阶段 4-管理和获取见解**：

| 通信      | 目标受众                                          |
| ------------------ | ------------------------------------------------- |
| 可用分析以及如何访问 | -应用技术所有者<br />-应用企业所有者 |

### <a name="migration-states-communication-dashboard"></a>迁移状态通信仪表板

与迁移项目的总体状态通信非常重要，因为它显示进度，并可帮助应用所有者，其应用将为迁移做好准备以进行迁移。 您可以使用 Power BI 或其他报表工具将一个简单的仪表板组合在一起，以在迁移期间提供应用程序状态的可见性。

可以考虑使用的迁移状态如下所示：

| 迁移状态       | 操作计划                                   |
| ---------------------- | --------------------------------------------- |
| **初始请求** | 查找应用并联系所有者以获取详细信息 |
| **评估完成** | 应用所有者评估应用要求并返回应用问卷</td>
| **正在进行配置** | 根据 Azure AD 开发管理身份验证所需的更改 |
| **测试配置成功** | 评估更改，并对测试环境中测试 Azure AD 租户的应用进行身份验证 |
| **生产配置成功** | 更改配置，使其适用于生产 AD 租户，并在测试环境中评估应用身份验证 |
| **完成/注销** | 将应用所做的更改部署到生产环境，并针对生产 Azure AD 租户执行 |

这将确保应用程序所有者知道应用程序迁移和测试计划是在其应用程序启动时迁移的，以及其他已迁移的应用程序的结果。 你还可以考虑提供指向 bug 跟踪器数据库的链接，以便所有者能够为要迁移的应用程序提供文件和查看问题。

### <a name="best-practices"></a>最佳实践

下面是我们的客户和合作伙伴的成功案例以及建议的最佳实践：

- 用于改进 Patriot 咨询的[迁移过程的五个技巧](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364)，这是我们的合作伙伴网络的成员，重点介绍 Azure Active Directory 如何帮助客户安全部署 Microsoft 云解决方案。

- Edgile 是一个侧重于 IAM 和风险管理解决方案的合作伙伴，[为你的 Azure AD 应用程序迁移制定风险管理策略](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488)。

## <a name="phase-1-discover-and-scope-apps"></a>阶段1：发现应用并确定其作用域

**应用程序发现和分析是一种很好的入门知识。** 你可能不知道所有内容，因此无法适应未知应用。

### <a name="find-your-apps"></a>查找你的应用

应用程序迁移中的第一个决策点是要迁移的应用（如果有的话）以及要弃用的应用。 始终有机会弃用你将不会在组织中使用的应用。 有多种方法可在组织中查找应用。 **发现应用时，请确保包括正在开发和计划的应用。在以后的所有应用中，请使用 Azure AD 进行身份验证。**

**使用 Active Directory 联合身份验证服务 (AD FS) 来收集正确的应用清单：**

- **使用 Azure AD Connect Health。** 如果你有 Azure AD Premium 许可证，我们建议部署 [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) 来分析你的本地环境中的应用使用情况。 你可以使用 [ADFS 应用程序报表](./migrate-adfs-application-activity.md) (预览) 来发现可以迁移的 adfs 应用程序并评估要迁移的应用程序的准备情况。 完成迁移后，请部署 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) ，使你能够在云中随时持续监视你的组织中的卷影 IT。

- **AD FS 日志分析**。 如果你没有 Azure AD Premium 许可证，我们建议使用 ADFS 来 Azure AD 基于 [PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration)的应用迁移工具。 请参阅 [解决方案指南](./migrate-adfs-apps-to-azure.md)：

[将 Active Directory 联合身份验证服务 (AD FS) 中的应用迁移到 Azure AD。](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>使用其他标识提供者 (Idp) 

对于其他标识提供程序 (例如 Okta 或 Ping) ，你可以使用其工具导出应用程序清单。 你可以考虑查看在与组织中的 web 应用相对应 Active Directory 上注册的服务原则。

### <a name="using-cloud-discovery-tools"></a>使用 cloud discovery 工具

在云环境中，您需要丰富的可见性、控制数据旅行和复杂的分析，以便在所有云服务中查找和对抗网络威胁。 你可以使用以下工具收集云应用清单：

- **Cloud Access Security Broker (CASB**) – [CASB](/cloud-app-security/) 通常与防火墙一起工作，以提供员工的云应用程序使用情况的可见性，并帮助保护你的公司数据免受网络安全威胁的攻击。 CASB 报表可帮助你确定组织中最常使用的应用，以及迁移到 Azure AD 的早期目标。

- **Cloud Discovery** -通过配置 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)，你可以查看云应用的使用情况，并且可以发现未批准或影子 IT 应用。

- **Api** -对于连接到云基础结构的应用程序，你可以使用这些系统上的 api 和工具开始对托管应用进行清点。 在 Azure 环境中：

  - 使用 [new-azurewebsite](/powershell/module/servicemanagement/azure/get-azurewebsite?view=azuresmps-4.0.0&redirectedfrom=MSDN&preserve-view=true)cmdlet 获取有关 Azure 网站的信息。

  - 使用 [new-azurermwebapp](/powershell/module/azurerm.websites/get-azurermwebapp?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.2.0&preserve-view=true)cmdlet 获取有关 Azure Web 应用的信息。

  - 可以使用 [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools)在 Windows 命令行中查找在 Microsoft IIS 上运行的所有应用。

  - 使用 [应用程序](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) 和 [服务主体](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ，获取有关 Azure AD 的目录中的应用程序和应用程序实例的信息。

### <a name="using-manual-processes"></a>使用手动过程

采用上述自动化方法后，你就可以在应用程序中使用一个良好的句柄。 但是，你可能会考虑执行以下操作，以确保在所有用户访问区域中都有良好的覆盖范围：

- 联系你的组织中的各个企业所有者，查找你的组织中使用的应用程序。

- 在代理服务器上运行 HTTP 检查工具或分析代理日志，以查看流量通常路由的位置。

- 查看网络日志的公司门户网站，查看用户最常访问的链接。

- 与管理人员或其他关键业务成员联系，确保你已涵盖关键业务应用程序。

### <a name="type-of-apps-to-migrate"></a>要迁移的应用类型

找到应用后，你将在组织中标识这些类型的应用：

- 已使用新式身份验证协议的应用

- 使用你选择的旧身份验证协议的应用进行现代化

- 使用你选择不实现现代化的旧身份验证协议的应用

- 新的业务线 (LoB) 应用

### <a name="apps-that-use-modern-authentication-already"></a>已使用新式身份验证的应用

已现代化的应用最有可能移动到 Azure AD。 这些应用已使用新式身份验证协议 (如 SAML 或 OpenID Connect) ，并可重新配置为使用 Azure AD 进行身份验证。

除了 [Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) 中的选项之外，这些应用程序还可以是已存在于你的组织中的应用，也可以是不属于 Azure AD 库 ([非库应用程序) ](./add-application-portal.md)中的任何第三方应用。

你选择进行现代化的旧版应用

对于想要实现现代化的传统应用，移动到 Azure AD 进行核心身份验证和授权，可解锁 [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) 和 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) 提供的所有功能和数据丰富功能。

建议从旧协议更新这些应用程序的 **身份验证堆栈代码** (例如 Windows 集成身份验证、Kerberos 约束委派、基于 HTTP 标头的身份验证) 到新式协议 (如 SAML 或 OpenID connect) 。

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>选择不实现现代化的旧版应用

对于某些使用旧身份验证协议的应用，有时现代化其身份验证不是出于业务原因所需要的。 其中包括以下类型的应用：

- 出于符合性或控制原因，本地保存的应用。

- 连接到本地标识或不想更改的联合身份验证提供程序的应用程序。

- 使用你没有计划迁移的本地身份验证标准开发的应用

Azure AD 可以为这些旧应用带来极大的好处，因为你可以启用新式 Azure AD 安全和监管功能，如 [多重身份验证](../authentication/concept-mfa-howitworks.md)、 [条件访问](../conditional-access/overview.md)、 [标识保护](../identity-protection/index.yml)、 [委派的应用程序访问](./access-panel-manage-self-service-access.md)权限，并对这些应用进行 [访问评审](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) ，无需接触应用！

首先使用 Azure AD [应用程序代理](./application-proxy-configure-single-sign-on-password-vaulting.md)**将这些应用扩展到云中**，使用简单的身份验证方法 (例如密码保管) ，以便快速迁移你的用户，或通过我们的合作伙伴与你可能已部署的应用程序交付控制器 [集成](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/)。

### <a name="new-line-of-business-lob-apps"></a>新的业务线 (LoB) 应用

通常会为组织内部使用开发 LoB 应用。 如果管道中有新应用，我们建议使用 [Microsoft 标识平台](../develop/v2-overview.md) 来实现 OpenID connect。

### <a name="apps-to-deprecate"></a>应用到弃用

无需清楚所有者和清除维护和监视的应用会给你的组织带来安全风险。 在以下情况时，请考虑弃用应用程序：

- 它们的功能对于其他系统 **来说是高度冗余** 的， **无企业所有者**

- 显然没有 **任何用法**。

当然， **不要弃用高影响关键业务应用程序**。 在这些情况下，请与业务所有者合作来确定正确的策略。

### <a name="exit-criteria"></a>退出条件

在此阶段中，你已成功完成以下操作：

- 很好地了解迁移范围内的系统 (在移动到 Azure AD 后，可以停用) 

- 应用列表，其中包括：

  - 这些应用从哪些设备连接到 o，以及在哪些设备上用户访问它们

  - 它们是迁移、弃用还是连接 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)。

> [!NOTE]
> 您可以下载 [应用程序发现工作表](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) 来记录要迁移到 Azure AD 身份验证的应用程序，以及要保留但使用 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)管理的应用程序。

## <a name="phase-2-classify-apps-and-plan-pilot"></a>阶段2：对应用进行分类并计划试点

对应用的迁移进行分类是一项重要的练习。 并非每个应用都需要同时迁移和转换。 收集每个应用的相关信息后，你可以合理化应该首先迁移哪些应用，这可能需要额外的时间。

### <a name="classify-in-scope-apps"></a>对作用域内应用进行分类

考虑这种情况的一种方法是，业务的关键程度、使用情况和生命周期，每个轴都依赖于多个因素。

### <a name="business-criticality"></a>业务关键性

对于每个业务，业务重要性将采用不同的维度，但你应该考虑的两个度量值是 **功能和功能** 和 **用户配置文件**。 使用具有冗余或过时功能的更高点值分配具有独特功能的应用。

![功能 & 功能和用户配置文件的 spectrums 示意图](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>使用情况

使用 **高使用率数字** 的应用程序的价值应高于使用率低的应用。 为具有外部、管理人员或安全团队用户的应用分配更高的值。 对于迁移组合中的每个应用，请完成这些评估。

![用户卷和用户广度的 spectrums 示意图](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

确定了业务重要性和使用情况的值之后，就可以确定 **应用程序的生命周期**，并创建优先级矩阵。 请参阅下面的一个矩阵：

![显示使用情况、预期的生命周期和业务重要性之间关系的三角形示意图](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>确定要迁移的应用的优先级

你可以根据组织的需求，选择开始与最低优先级应用或最高优先级应用的应用迁移。

在你可能没有使用 Azure AD 和标识服务的情况下，请考虑首先将 **最低优先级的应用程序** 迁移到 Azure AD。 这将最大程度地降低业务影响，并且可以构建动力。 成功移动这些应用并获得利益干系人的信心后，可以继续迁移其他应用。

如果没有明确的优先级，你应考虑先移动 [Azure AD 库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) 中的应用，并支持多个标识提供者 (ADFS 或 Okta) ，因为它们更易于集成。 这些应用很可能是组织中 **优先级最高的应用** 。 为了帮助将 SaaS 应用程序与 Azure AD 集成，我们开发了一个 [教程](../saas-apps/tutorial-list.md) 集合，指导你完成配置。

当你有截止时间迁移应用程序时，这些最高优先级的应用存储桶会占用大量工作负荷。 最终可以选择较低优先级的应用，因为即使你已移动截止时间，它们也不会更改成本。 即使您必须续订许可证，它也会很小。

除了此分类以及迁移的紧急性，你还可以考虑 **制定一个迁移计划** ，应用所有者必须在该计划中进行应用迁移。 在此过程结束时，应该有一个列表，其中列出了用于迁移的优先级存储桶中的所有应用程序。

### <a name="document-your-apps"></a>记录应用

首先，请首先收集有关你的应用程序的关键详细信息。 [应用程序发现工作表](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)可以帮助你快速做出迁移决策，并在无需任何时间的情况上获取对业务组的建议。

做出迁移决策的重要信息包括：

- **应用名称** –此应用如何称为业务？

- **应用类型** –是否为第三方 SaaS 应用？ 自定义业务线 web 应用？ API？

- **业务重要程度** –高重要性？ 低级? 或介于之间的某个位置？

- **用户访问量** –每个人是否都能访问此应用程序或只是少数人员？

- **计划的生命周期** -此应用的持续时间是多少？ 不到6个月？ 超过2年？

- **当前标识提供者** –此应用的主要 IdP 是什么？ 或者它是否依赖于本地存储？

- **身份验证方法** –应用是否使用开放标准进行身份验证？

- **是否计划更新应用代码** –计划内或活动开发下的应用？

- **是否计划将应用保留在本地** -是否要在数据中心长期保留应用？

- **应用是否依赖于其他应用或 api** –应用当前是否调入其他应用或 api？

- **应用是否在 Azure AD 库** –应用当前是否已与 [Azure AD 库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)集成？

其他数据将帮助你稍后使用，但你无需做出直接迁移决策：

- **应用 URL** –用户在何处访问应用？

- **应用说明** –应用功能的简要说明是什么？

- **应用所有者** –业务人员是应用的主要 POC

- **一般注释或备注** –有关应用或企业所有权的任何其他一般信息

当你对应用程序进行了分类并记录了详细信息后，请确保获得企业所有者购买计划的迁移策略。

### <a name="plan-a-pilot"></a>规划试点

你为试验选择的应用 () 应表示组织的主要标识和安全要求，你必须明确地从应用程序所有者处购买。 试点通常在单独的测试环境中运行。 请参阅 "部署计划" 页上 [的最佳实践](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 指导。

**别忘了外部合作伙伴。** 请确保它们参与迁移计划和测试。 最后，确保在出现重大问题时，它们可以访问你的支持人员。

### <a name="plan-for-limitations"></a>计划限制

虽然某些应用程序可以轻松迁移，但其他应用程序可能需要更长的时间，因为有多个服务器或实例。 例如，SharePoint 迁移可能需要更长的时间，因为自定义登录页。

许多 SaaS 应用供应商对更改 SSO 连接收费。 请咨询并规划这一问题。

Azure AD 还具有应注意的 [服务限制和限制](../enterprise-users/directory-service-limits-restrictions.md) 。

### <a name="app-owner-sign-off"></a>应用所有者签署

业务关键且广泛使用的应用程序可能需要一组试验用户来测试试验阶段的应用。 在预生产或试点环境中测试应用后，请确保应用程序企业所有者在迁移应用程序之前对其进行身份验证，并使用所有用户 Azure AD 进行身份验证。

### <a name="plan-the-security-posture"></a>规划安全状况

在开始迁移过程之前，请花些时间来完全考虑你希望为企业标识系统开发的安全状况。 这基于收集这些有用的信息集： **标识和数据、访问数据的人员、设备和位置**。

### <a name="identities-and-data"></a>标识和数据

大多数组织对标识和数据保护有特定要求，这些要求因行业部门和组织内的工作职能而异。 有关建议，请参阅 [标识和设备访问配置](/microsoft-365/enterprise/microsoft-365-policies-configurations) ，其中包括一组规定的 [条件性访问策略](../conditional-access/overview.md) 和相关功能。

你可以使用此信息来保护对与 Azure AD 集成的所有服务的访问。 这些建议与 Microsoft 安全分数和 [Azure AD 中的身份评分](../fundamentals/identity-secure-score.md)相一致。 评分有助于：

- 客观衡量标识安全状况

- 规划标识安全改进

- 审查改进措施的成败

这还将帮助你实现 [五个步骤来保护标识基础结构](../../security/fundamentals/steps-secure-identity.md)。 使用本指南作为组织的起点，并调整策略以满足组织的特定要求。

### <a name="who-is-accessing-your-data"></a>谁在访问你的数据？

Azure AD 支持的应用和资源有两种主要类别：

- **内部：** 在标识提供者内拥有帐户的员工、承包商和供应商。 对于经理或领导与其他员工的不同规则，这可能需要进一步的透视。

- **外部：** 供应商、供应商、分销商或其他业务合作伙伴，它们通过 [AZURE AD B2B 协作](../external-identities/what-is-b2b.md)与您的组织进行交互。

你可以为这些用户定义组并以不同方式填充这些组。 你可以选择管理员必须手动将成员添加到组中，或者可以启用 selfservice 组成员身份。 可以使用 [动态组](../enterprise-users/groups-dynamic-membership.md)根据指定的条件，建立自动将成员添加到组中的规则。

外部用户还可以引用需要特别注意的客户。 [Azure AD B2C](../../active-directory-b2c/overview.md)，单独的产品支持客户身份验证。 但是，这超出了本文的讨论范围。

### <a name="devicelocation-used-to-access-data"></a>用于访问数据的设备/位置

用户用于访问应用程序的设备和位置也很重要。 物理连接到公司网络的设备更安全。 从网络外部的 VPN 连接可能需要进行审查。

![显示用户位置与数据访问之间的关系的关系图](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

考虑到资源、用户和设备的这些方面，你可以选择使用 [Azure AD 的条件性访问](../conditional-access/overview.md) 功能。 条件访问超出用户权限：它基于因素的组合，例如用户或组的标识、用户所连接到的网络、所使用的设备和应用程序以及尝试访问的数据的类型。 授予用户的访问权限可适应这一组更广泛的条件。

### <a name="exit-criteria"></a>退出条件

在此阶段中，你将成功完成以下操作：

- 了解你的应用
  - 已完整记录要迁移的应用
  - 基于业务关键性、使用情况量和生命周期设置优先级的应用

- 选择了表示您的试点要求的应用程序

- 企业所有者购买优先级和策略

- 了解你的安全状况需求以及如何实现它们

## <a name="phase-3-plan-migration-and-testing"></a>阶段3：规划迁移和测试

获得业务知情后，下一步就是开始将这些应用迁移到 Azure AD 身份验证。

### <a name="migration-tools-and-guidance"></a>迁移工具和指南

使用下面的工具和指南来执行将应用程序迁移到 Azure AD 所需的具体步骤：

- **一般迁移指南** –使用 [Azure AD 应用迁移工具包](./migration-resources.md) 中的白皮书、工具、电子邮件模板和应用程序问卷来发现、分类和迁移应用程序。

- **SaaS 应用程序** -请参阅 [数百个 saas 应用教程](../saas-apps/tutorial-list.md) 列表和完整的 [Azure AD SSO 部署计划](https://aka.ms/ssodeploymentplan) ，逐步完成端到端过程。

- **本地运行的应用程序** –了解 [Azure AD 应用程序代理的所有信息](./application-proxy.md) ，并使用完整的 [Azure AD 应用程序代理部署计划](https://aka.ms/AppProxyDPDownload) 来快速完成。

- **你正在开发的应用程序** -请参阅我们的分步 [集成](../develop/quickstart-register-app.md) 和 [注册](../develop/quickstart-register-app.md) 指南。

迁移后，你可以选择发送通信通知用户成功部署，并提醒他们需要执行的任何新步骤。

### <a name="plan-testing"></a>规划测试

在迁移过程中，你的应用可能已具有在常规部署过程中使用的测试环境。 你可以继续使用此环境进行迁移测试。 如果测试环境当前不可用，则可以使用 Azure App Service 或 Azure 虚拟机来设置一个，具体取决于应用程序的体系结构。 你可以选择设置单独的测试 Azure AD 租户，以在开发应用配置时使用。 此租户将以干净状态启动，并且不会配置为与任何系统同步。

您可以通过使用测试用户登录来测试每个应用程序，并确保所有功能与迁移之前的功能相同。 如果在测试期间确定用户将需要更新其 [MFA](/active-directory/authentication/howto-mfa-userstates) 或 [SSPR](../authentication/tutorial-enable-sspr.md)设置，或者在迁移过程中添加了此功能，请确保将其添加到最终用户通信计划中。 请参阅 [MFA](https://aka.ms/mfatemplates) 和 [SSPR](https://aka.ms/ssprtemplates) 最终用户通信模板。

迁移应用后，请在 [Azure 门户](https://aad.portal.azure.com/) 中进行测试，以测试迁移是否成功。 请根据以下说明进行操作：

- 从列表中选择 " **企业应用程序" " &gt; 所有应用程序** " 和 "查找应用"。

- 选择 " **管理 &gt; 用户和组** "，将至少一个用户或组分配到应用。

- 选择 " **管理 &gt; 条件性访问**"。 查看策略列表，并确保不会使用 [条件性访问策略](../conditional-access/overview.md)阻止对应用程序的访问。

根据你配置应用的方式，验证 SSO 是否正常工作。

| 身份验证类型      | 测试                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | 选择 " **企业应用程序" &gt; 权限** ，并确保你同意在你的组织中的应用程序的用户设置中使用该应用程序。 |
| **基于 SAML 的 SSO** | 使用 "**单一登录**" 下的 "[测试 SAML 设置](./debug-saml-sso-issues.md)" 按钮。 |
| **基于密码的 SSO** | 下载并安装 [MyApps 安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。 此扩展可帮助你启动组织的任何需要使用 SSO 过程的云应用。 |

| **[应用程序代理](./application-proxy.md)** |确保连接器正在运行并已分配给应用程序。 请访问 [应用程序代理故障排除指南](./application-proxy-troubleshoot.md) 以获取进一步的帮助。 |

### <a name="troubleshoot"></a>疑难解答

如果遇到问题，请查看我们的 [应用故障排除指南](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) 以获取帮助。 另请参阅 [登录到自定义开发的应用程序时出现的问题](./application-sign-in-problem-federated-sso-gallery.md)。

### <a name="plan-rollback"></a>规划回滚

如果迁移失败，最佳策略是回滚和测试。 可以采取以下步骤来缓解迁移问题：

- 获取应用程序的现有配置的 **屏幕截图**。 如果必须重新配置应用程序，可以查看回来。

- 如果云身份验证出现问题，你还可以考虑 **提供到旧身份验证的链接**。

- 在完成迁移之前，请不要更改先前的标识提供者的 **现有配置** 。

- 首先迁移 **支持多个 idp 的应用程序**。 如果出现问题，你始终可以更改为首选的 IdP 配置。

- 在出现问题时，请确保你的应用程序体验有一个 **反馈按钮** 或指向你的 **支持人员** 的指针。

### <a name="exit-criteria"></a>退出条件

在此阶段中，你已成功完成以下操作：

- 确定每个应用的迁移方式

- 查看迁移工具

- 计划测试，包括测试环境和组

- 计划回滚

## <a name="phase-4-plan-management-and-insights"></a>阶段4：规划管理和见解

迁移应用后，必须确保：

- 用户可以安全地访问和管理

- 可以深入了解使用情况和应用运行状况

建议你根据组织的需要采取以下措施。

### <a name="manage-your-users-app-access"></a>管理用户的应用访问

迁移应用后，你可以通过多种方式丰富你的用户体验

**使应用可检测**

将 **用户指向** [MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)门户体验。 在这里，他们可以访问所有基于云的应用、使用 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)提供的应用以及使用 [应用程序代理](./application-proxy.md) 的应用（前提是他们有权访问这些应用）。


可以指导用户了解如何发现其应用：

- 使用 [现有的单一登录](./view-applications-portal.md) 功能将 **用户链接到任何应用**


- 启用对应用 [程序的自助应用程序访问](./manage-self-service-access.md)，并 **让用户添加组织的应用**

- 将 [应用程序从最终用户隐藏](./hide-application-from-user-portal.md) (默认 Microsoft 应用程序或其他应用程序) ，以 **使应用程序所需的应用程序更容易发现**

### <a name="make-apps-accessible"></a>使应用程序可访问

**允许用户从其移动设备访问应用**。 用户可以在其 [iOS](./hide-application-from-user-portal.md) 7.0 或更高版本或 [Android](./hide-application-from-user-portal.md) 设备上使用 Intune 管理的浏览器访问 MyApps 门户。

用户可以下载 **Intune 托管的浏览器**：

- **对于 Android 设备**，从 [Google play 商店](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)

- **对于 apple 设备**，可通过 [apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)下载 "[我的应用" 移动应用（适用于 iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653) ）

**允许用户通过浏览器扩展打开其应用。**

用户可以下载[Chrome、](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)或[Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)中[的 MyApps 安全登录扩展](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)，并可以直接从浏览器栏启动应用以执行以下操作：

- **搜索其应用并显示最近使用的应用**

- 自动将 [应用程序代理](./application-proxy.md)中配置的 **内部 url 转换** 为相应的外部 url。 用户现在可以使用他们熟悉的链接，而不管他们在哪里。

**允许用户从 Office.com 打开其应用。**

用户可以使用 [Office.com](https://www.office.com/) **搜索其应用，并将最近使用的应用程序** 从其工作位置立即显示出来。

### <a name="secure-app-access"></a>安全应用程序访问

Azure AD 提供了一个集中式访问位置来管理已迁移的应用。 请中转到 [Azure 门户](https://portal.azure.com/) ，并启用以下功能：

- **保护用户对应用的访问。** 启用 [条件性访问策略](../conditional-access/overview.md)或 [Identity Protection](../identity-protection/overview-identity-protection.md)，以根据设备状态、位置等来保护用户对应用程序的访问。

- **自动设置。** 使用用户需要访问的各种第三方 SaaS 应用程序设置 [用户的自动预配](../app-provisioning/user-provisioning.md) 。 除了创建用户标识以外，它还包括维护和删除用户标识，因为状态或角色发生更改。

- **委托用户访问****管理**。 根据需要，启用对应用的自助应用程序访问，并 *分配一个业务审批者来批准对这些应用的访问*。 对分配给应用集合的组使用 [自助服务组管理](../enterprise-users/groups-self-service-management.md)。

- **委派管理员访问权限。** 使用 **目录角色** 向用户分配管理员角色 (例如，应用程序管理员、云应用程序管理员或应用程序开发人员) 。

### <a name="audit-and-gain-insights-of-your-apps"></a>审核并深入了解你的应用

你还可以使用 [Azure 门户](https://portal.azure.com/) 从集中的位置审核所有应用。

- 使用 **企业应用程序****审核您的应用** 程序，从 [Azure AD 报告 API](../reports-monitoring/concept-reporting-api.md)审核或访问相同的信息以集成到您最喜爱的工具中。

- 使用 **企业应用程序、** 使用 OAuth/OpenID connect 的应用程序的权限 **查看应用程序的权限**。

- 使用 **企业应用程序和登录****获取登录** 信息。从 [Azure AD 报告 API](../reports-monitoring/concept-reporting-api.md)访问相同的信息。

- 使用 [Azure AD PowerBI 内容包](../reports-monitoring/howto-use-azure-monitor-workbooks.md)**直观显示应用的使用情况**

### <a name="exit-criteria"></a>退出条件

在此阶段中，你将成功完成以下操作：

- 提供对用户的安全应用程序访问

- 管理以审核并深入了解已迁移的应用

### <a name="do-even-more-with-deployment-plans"></a>部署计划甚至更多的操作

部署计划将引导你完成 Azure AD 解决方案（包括应用程序迁移方案）的业务价值、规划、实施步骤和管理。 它们汇集了开始部署和获取 Azure AD 功能的价值所需的所有内容。 部署指南包括 Microsoft 推荐的最佳做法、最终用户通信、计划指南、实施步骤、测试用例等内容。

许多 [部署计划](../fundamentals/active-directory-deployment-plans.md) 均可供您使用，我们一直在进行更多的部署！

### <a name="contact-support"></a>联系支持人员

请访问以下支持链接，创建或跟踪支持票证和监视运行状况。

- **Azure 支持：** 可以调用 [Microsoft 支持部门](https://azure.microsoft.com/support) 并为任何 Azure 打开票证

标识部署问题取决于与 Microsoft 企业协议。

- **FastTrack**：如果你已购买企业移动性和安全性 (EMS) 或 Azure AD Premium 许可证，你可以从 [FastTrack 程序](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)接收部署帮助。

- **与产品工程团队合作：** 如果你正在使用数百万个用户进行一次重大的客户部署，你将有权从 Microsoft 帐户团队或你的云解决方案架构师那里获得支持。 根据项目的部署复杂性，可以直接使用 [Azure Identity Product 工程团队。](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure AD 标识博客：** 订阅 [Azure AD 标识博客](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) ，随时了解由标识工程团队直接提供的所有最新产品公告、深度深入和路线图信息。
