---
title: 建立强大标识基础的四个步骤 - Azure AD
description: 本主题介绍了混合标识客户建立强大标识基础的四个步骤。
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24ea1fa02a70e63c8eaab21acff1d1c7b625e9a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727724"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>使用 Azure Active Directory 建立强大标识基础的四个步骤

由于应用向云端的快速移动，管理对应用和数据的访问不能再依赖传统的网络安全边界策略，如外围网络和防火墙。 如今，组织必须信任其标识解决方案，从而控制访问组织应用程序和数据的权限。 越来越多的组织允许员工自带设备工作，并在任何可以连接互联网的地方使用他们的设备。 确保这些设备的合规性和安全性已成为组织选择标识解决方案的一个重要考虑因素。 在当今的数字化工作场所，[标识管理是任何组织转向云端的主要控制手段](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)。

在采用 Azure Active Directory (Azure AD) 混合标识解决方案的过程中，组织可以通过自动化、授权、自助服务和单点登录功能来提高工作效率。 通过这一高级功能，组织的员工可在任何工作地点访问公司资源，同时组织的 IT 团队可通过确保合适的人有合适的资源访问权来管控这种访问，从而保证安全性。

根据我们的经验，这份最佳做法清单将有助于快速部署建议的行动，在组织中建立一个强化的标识基础：

* 轻松连接到应用程序
* 自动为每个用户建立一个标识
* 安全地授权用户
* 落实实施你的见解

## <a name="step-1---connect-to-apps-easily"></a>第一步 - 轻松连接到应用程序

将应用程序与Azure AD 连接后，可以通过启用单点登录 (SSO) 和进行用户预配来提高终端用户的生产力和安全性。 借助 Azure AD，通过在一个位置管理你的应用程序，可以最大程度地减少管理开销，并为安全和合规性策略实现单点控制。

本节介绍了管理用户对应用程序的访问、启用对内部应用程序的安全远程访问的选项，以及将应用程序迁移到 Azure AD 的好处。

### <a name="make-apps-available-to-your-users-seamlessly"></a>让用户无缝使用应用程序

Azure AD 使管理员能够[将应用程序添加到](../manage-apps/add-application-portal.md)[Azure 门户](https://portal.azure.com/)中的企业应用程序库中。 将应用程序添加到企业应用程序库让用户更便于配置应用程序来使用 Azure AD 获得标识管理服务。 通过 Azure AD，可使用条件访问策略管理用户对应用的访问，并为应用配置单点登录 (SSO)，这样用户就不必重复输入密码，而是自动登录到本地应用和基于云的应用。

将应用程序添加到 Azure AD 库后，用户可以查看分配给他们的应用，并根据需要搜索和请求其他应用。 Azure AD 为用户提供[多种方法](../manage-apps/end-user-experiences.md)来访问其应用：

* 访问面板/我的应用
* Microsoft 365 应用启动器
* 直接登录联合应用
* 直接登录链接

如要了解有关用户访问应用程序的更多信息，请参阅本文中的 **步骤 3--赋权用户**。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>将应用从 Active Directory 联合身份验证服务迁移到 Azure AD

将单点登录配置从 Active Directory 联合身份验证服务 (ADFS) 迁移到 Azure AD，可以实现更多的安全功能、更一致的可管理性和协作。 为获得最佳效果，我们建议将应用程序从 ADFS 迁移到 Azure AD。 将您的应用程序身份验证和授权引入 Azure AD 可提供以下优势：

* 管理成本
* 管理风险
* 提高工作效率
* 解决合规性和管治问题

如要详细信息，请参阅[将应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper) 白皮书。

### <a name="enable-secure-remote-access-to-apps"></a>启用安全远程访问应用程序

[Azure AD 应用程序代理](../app-proxy/what-is-application-proxy.md)为组织提供了一种简单的解决方案，安全地向需要访问内部应用的远程用户发布本地应用。 单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云和本地的应用程序。

Azure AD 应用程序代理具有以下安全优势：

* 将 Azure AD 扩展到本地资源
  * 云级安全性和保护
  * 可轻松启用条件性访问和多重身份验证等功能
* 外围网络中没有 VPN 和传统反向代理解决方案等组件
* 不需要任何入站连接
* 支持在云中和本地跨设备、资源与应用进行单一登录 (SSO)
* 使最终用户始终保持较高的工作效率

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>通过 Microsoft Cloud App Security 发现影子 IT

在现代企业中，IT 部门通常未注意用户用于完成其工作的所有云应用程序。 当 IT 管理员询问员工使用的云应用数量时，平均答案为 30 或 40 个云应用。 实际上，组织中的员工平均使用了超过 1000 个独立的应用程序。 80% 的员工使用了未获批准的应用，这些应用未经审核，可能不符合安全性和合规性策略。

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) 可帮助确定受用户欢迎的应用程序，这些应用程序可能被批准并添加到企业应用程序库中，以便用户受益于 SSO 和条件性访问等功能。

<em>“Cloud App Security 可帮助我们支持有助于保护 Accenture 的基础安全策略，正确地使用云和 SaaS 应用程序。”</em> --- [Accenture 信息安全总经理 John Blasi](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了检测影子 IT 外，MCAS 还可以确定应用程序的风险级别，防止未经授权访问公司数据、可能发生的数据泄漏和应用程序固有的其他安全风险。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步骤 2 - 自动为每个用户建立一个标识

将本地和基于云的目录一起引入 Azure AD 的混合标识解决方案中，用户可以通过在云中预配现有标识来重复使用现有的本地 Active Directory 投资。 该解决方案将本地标识与 Azure AD 同步，而 IT 部门将本地 Active Directory 与任何现有的治理解决方案一起运行，作为标识的主要真实来源。 Microsoft Azure AD 混合标识解决方案跨越了本地和基于云的功能，创建了一个通用的用户标识，用于对所有资源进行身份验证和授权，而不论其位置如何。

将本地目录与 Azure AD 集成可提高用户的工作效率，并通过提供访问云和本地资源的通用标识，防止用户在不同应用和服务中使用多个账户。 对于最终用户和 IT 人员而言，使用多个帐户是一个痛点。 从最终用户的角度来看，具有多个帐户意味着必须记住多个密码。 为避免此种情况，许多用户为每个账户设置相同的密码，从安全角度来看，这是不可取的。 从 IT 人员角度来看，重复使用相同的密码会导致更多的密码重置和技术支持人员成本以及最终用户的投诉。

Azure AD Connect 这种工具用于将本地标识同步到 Azure AD，然后使用此工具访问云应用程序。 标识同步到 Azure AD 后，可将其预配到 SaaS 应用程序（如 Salesforce 或 Concur）。

本节中列出了为云提供高可用性、现代身份验证和减少本地占用量的建议。

> [!NOTE]
> 如果要了解有关 Azure AD Connect 的详细信息，请参阅[什么是 Azure AD Connect 同步？](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>为 Azure AD Connect 设置暂存服务器，并保持最新状态

Azure AD Connect 在预配过程中起到重要作用。 如果同步服务器出于任何原因进入脱机状态，则不会在云中更新对本地的更改，从而导致用户访问权限问题。 必须定义一个故障转移策略，便于管理员能够在同步服务器脱机后快速恢复同步。

建议为 Azure AD Connect 部署单独的[临时服务器](./how-to-connect-sync-staging-server.md) ，确保在主 Azure AD Connect 服务器脱机的情况下也能提供高可用性。 部署服务器后，通过简单的配置交换机，管理员可将暂存服务器 "升级" 到生产服务器。 通过将备用服务器配置为暂存模式，还可测试和部署新的配置更改，并在解除旧服务器的授权的情况下引入新的服务器。

> [!TIP]
> 定期更新 Azure AD Connect。 因此，强烈建议用户将暂存服务器保持最新状态，以便充分利用性能改进、bug 修复和每个新版本提供的新功能。

### <a name="enable-cloud-authentication"></a>启用云身份验证

运用了本地 Active Directory 的组织应使用 Azure AD Connect 将其目录扩展到 Azure AD，并配置适当的身份验证方法。 为组织[选择正确的身份验证方法](./choose-ad-authn.md)是将应用程序迁移到云的第一个步骤。 这一步控制了对所有云数据和资源的访问权限，因此很关键。

在 Azure AD 中为本地目录对象启用云身份验证的最简单和推荐方法是启用[密码哈希同步](./how-to-connect-password-hash-synchronization.md) (PHS)。 此外，某些组织可能会考虑启用[传递身份验证](./how-to-connect-pta-quick-start.md) (PTA)。

无论选择 PHS 还是 PTA，都需要启用[无缝单一登录](./how-to-connect-sso.md) 使用户在使用企业网络中的 Windows 7 和 Windows 8 设备时，无需在应用中不断输入用户名和密码即可访问云应用。 不使用单一登录，用户必须记住应用程序特定的密码并登录每个应用程序。 IT 人员需要为每个应用程序（如 Microsoft 365、Box 和 Salesforce）创建和更新用户帐户。 用户需要记住各个密码，并且花时间登录每个应用程序。 为整个企业提供标准化的单一登录机制对于获得最佳用户体验、降低风险、报告和治理能力至关重要。

对于已使用 AD FS 或其他本地身份验证提供程序的组织，随着标识提供程序迁移到 Azure AD，可以降低复杂性并提高可用性。 除非有使用联合身份验证的特定用例，否则我们建议从联合身份验证迁移到 PHS 和无缝 SSO 或 PTA 和无缝 SSO，以减少本地占用量以及实现云服务的灵活性，改善用户体验。 如需了解详细信息，请参阅[从联合身份验证迁移到 Azure Active Directory 密码哈希同步](./migrate-from-federation-to-cloud-authentication.md)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>启用自动撤销帐户

为应用程序启用自动预配和取消设置是在多个系统之间管理标识生命周期的最佳策略。 Azure AD 支持根据政策将用户帐户[自动预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)到各种使用率高的 SaaS 应用程序（如 ServiceNow 和 Salesforce）以及其他实现 [SCIM 2.0 协议](../app-provisioning/use-scim-to-provision-users-and-groups.md)的应用程序，也支持用户自动取消设置。 与需要自定义代码或手动上传 CSV 文件的传统预配解决方案不同，这种预配服务托管在云中，并具有可使用 Azure 门户进行设置和管理的预集成连接器。 自动取消设置的主要优点是，它可以在用户离开组织时立即从关键 SaaS 应用中删除用户的标识，从而帮助组织确保安全。

若要了解有关用户帐户自动预配及其工作原理的详细信息，请参阅 [Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](../app-provisioning/user-provisioning.md)。

## <a name="step-3---empower-your-users-securely"></a>步骤 3 - 安全地授权用户

在当今的数字工作环境中，将安全性与工作效率进行平衡非常重要。 但是，最终用户常常退回使用那些会降低工作效率和延缓云应用访问速度的安全措施。 为了帮助解决这种情况，Azure AD 提供了自助服务功能，使用户能够在最大程度地降低管理开销的同时保持较高的工作效率。

本节列出了一些建议，这些建议可通过在保持警惕的同时赋予用户权限来消除组织中的摩擦。

### <a name="enable-self-service-password-reset-for-all-users"></a>为所有用户启用自助式密码重置服务

IT 管理员可以通过 Azure 的[自助密码重置](../authentication/tutorial-enable-sspr.md) (SSPR)，让用户方便重置或解锁其密码或帐户，而无需管理员的干预。 系统提供详细的报告，用于跟踪用户访问系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。 但是，当用户启用 Azure AD Connect [本地集成](../authentication/concept-sspr-howitworks.md#on-premises-integration)时，还可以选择分离这两个操作，使用户无需重置密码即可解锁其帐户。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>确保所有用户都注册了 MFA 和 SSPR

Azure 提供可供用户和其组织使用的报告，以确保用户已注册 MFA 和 SSPR。 在此过程中，可能需要建议尚未注册的用户及时注册。

MFA [登录报告](../authentication/howto-mfa-reporting.md)包含有关 MFA 使用情况的信息，可帮助用户深入了解如何在组织中使用 MFA。 有权限访问 Azure AD 的登录活动（以及审核和风险检测），对于故障排除、使用情况分析和取证调查至关重要。

同样，[自助式密码管理报告](../authentication/howto-sspr-reporting.md)可用于确定已注册或未注册 SSPR 的用户。

### <a name="self-service-app-management"></a>自助服务应用管理

在用户可从其访问面板自行发现应用程序之前，需要启用[自助应用程序访问](../manage-apps/access-panel-manage-self-service-access.md)来访问希望用户自我发现并请求访问的任何应用程序。 自助应用程序访问是帮助用户自行发现应用程序的绝佳方式，可选择性地允许业务组批准对这些应用程序的访问。 可允许业务组直接从其访问面板，管理分配给用户的[密码单一登录应用程序](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app)的凭据。

### <a name="self-service-group-management"></a>自助组管理

当使用组时，最好将用户分配到应用程序，因为它们具有极大的灵活性和大规模管理能力：

* 基于属性使用动态组成员资格
* 授权应用程序所有者

通过 Azure AD，可以使用安全组和 Microsoft 365 组来管理对资源的访问。 这些组由组的所有者管理，组所有者可以批准或拒绝成员资格请求并授权对成员资格的控制。 此功能称为[自助服务组管理](../enterprise-users/groups-self-service-management.md)，未分配管理角色的组所有者可以创建和管理组，而不必依靠管理员来处理他们的请求，从而节省了时间。

## <a name="step-4---operationalize-your-insights"></a>步骤 4 - 落实实施你的见解

审核和记录与安全相关的事件和相关警报是有效策略的重要组成部分，可确保用户较高的工作效率和组织的安全性。 安全日志和报告可以帮助解答以下问题：

* 你是否使用的是所付费的服务？
* 我的租户中是否有发生任何可疑或恶意的情况？
* 发生安全事件时，谁受到了影响？

安全日志和报告提供可疑活动的电子记录，并帮助检测可能指示试图或已成功的外部网络渗透以及内部攻击的模式。 可以使用审核来监控用户活动、记录法规遵从性以及进行取证分析等。 警报提供安全事件的通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>为操作分配最低特权的管理员角色

在考虑操作方法时，需要考虑几个级别的管理。 第一级是将管理工作任务委任给你的全局管理员。 分配全局管理员角色可能较为适合小型公司。 但对于拥有技术支持人员和负责特定任务的管理员的大型组织来说，分配全局管理员的角色可能会带来安全风险，因为这些全局管理员可能要管理高于和超出其管理能力的任务。

在这种情况下，应考虑下一级的管理。 使用 Azure AD，可将最终用户指定为“有限权限管理员”，这些用户能以较低权限的角色管理任务。 例如，可以向技术支持人员分配[安全读者](../roles/permissions-reference.md#security-reader)角色，使其能够使用只读访问权限来管理与安全相关的功能。 或者，可将[身份验证管理员](../roles/permissions-reference.md#authentication-administrator)角色分配给个人，使其能够重置非密码凭据或读取和配置 Azure 服务运行状况。

有关此角色的详细信息，请参阅[Azure Active Directory 中的管理员角色权限](../roles/permissions-reference.md)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 监控混合组件 (Azure AD Connect 同步、AD FS)

Azure AD Connect 和 AD FS 是有可能打破生命周期管理和身份验证并最终导致中断的关键组件。 因此，需要部署 Azure AD Connect Health 来监控和报告这些组件。

如要了解详细信息，请参阅[使用 Azure AD Connect Health 监控 AD FS](./how-to-connect-health-adfs.md)。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure Monitor 收集用于分析的数据日志

[Azure Monitor](../../azure-monitor/overview.md) 是适用于所有 Azure AD 日志的统一监控门户，可提供深度洞察、高级分析和智能机器学习。 借助 Azure Monitor，你可以在门户中并通过 Api 使用指标和日志，以更好地了解资源的状态和性能。 它在门户中启用了单一的管理平台，同时通过 API 和数据导出选项实现了广泛的产品集成，支持传统的第三方 SIEM 系统。 Azure Monitor 还可以配置警报规则以获得通知，或对影响资源的问题执行自动操作。

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>为领导和日常工作创建自定义仪表板

没有 SIEM 解决方案的组织可以下载 Azure AD [Power BI 内容包](../reports-monitoring/howto-use-azure-monitor-workbooks.md)。 Power BI 内容包包含预构建的报告，可帮助你理解你的用户如何采用和使用 Azure AD 功能，便于你能够深入了解目录中的所有活动。 另外，还可以创建自己的[自定义仪表板](/power-bi/service-dashboards)，并与领导团队共享来报告日常活动。 通过仪表板可以很好地监控你的业务，并一目了然地查看所有最重要的指标。 仪表板上的可视化效果可能来自一个或许多个基础数据集，也可能来自一个或多个基础报表。 仪表板将本地数据和云数据合并到一起，提供合并视图（无论数据源自哪里）。

![Power BI 自定义仪表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>了解支持调用驱动程序

当实施本文中所述的混合标识解决方案时，应最终注意到支持调用的缩减。 通过实施 Azure 的自助服务密码重置，可以缓解忘记密码和账户锁定等常见问题，同时启用自助服务应用访问，可让用户自行发现并请求访问应用程序，而无需依赖 IT 人员。

如果没有看到支持调用减少，建议分析支持调用驱动程序，尝试确认是否已正确配置 SSPR 或自助服务应用程序访问，或者是否有任何其他可以系统地解决的新问题。

*“我们在数字化转型过程中，需要一个可靠的标识和访问管理提供商，以促进我方、合作伙伴和云服务提供商之间无缝而安全的集成，从而建立一个有效的生态系统；Azure AD 是最佳选择，为我们提供了所需的功能和可视性，使我们能够检测和应对风险。”* --- [Aramex 全球信息安全总监 Yazan Almasri](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>监视应用程序的使用情况以深化见解

除了发现影子 IT 外，使用 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) 监视组织中应用程序的使用情况还有助于你的组织充分利用云应用程序的前景。 它可以帮助你通过提高活动的可见性来控制资产，并增强对整个云应用程序的关键数据的保护。 借助 MCAS 监视组织中的应用程序使用情况可帮助你解答以下问题：

* 员工使用哪些未批准的应用来存储数据？
* 在云中存储敏感数据的位置和时间？
* 谁在访问云中的敏感数据？

*“借助 Cloud App Security，我们可以及时发现异常并采取措施。”* --- [Accenture 信息安全高级经理 Eric LePenske](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>摘要

实现混合标识解决方案有许多方面，但以上四个步骤将帮助你快速完成标识基础结构，使用户能够提高工作效率和安全性。

* 轻松连接到应用程序
* 自动为每个用户建立一个标识
* 安全地授权用户
* 落实实施你的见解

我们希望此文档能够有效地指导你的组织建立一个强大的标识基础结构。

## <a name="identity-checklist"></a>标识清单

我们建议在组织中开始构建更强大的标识基础结构之前，先打印以下清单作为参考。

### <a name="today"></a>今天

|完成了吗？|项|
|:-|:-|
||针对组的试点自助服务密码重置 (SSPR)|
||使用 Azure AD Connect Health 监视混合组件|
||为操作分配最低特权的管理员角色|
||通过 Microsoft Cloud App Security 发现影子 IT|
||使用 Azure Monitor 收集数据日志进行分析|

### <a name="next-two-weeks"></a>接下来两周

|完成了吗？|项|
|:-|:-|
||使应用可供用户使用|
||适用于所选 SaaS 应用的试点 Azure AD 预配|
||为 Azure AD Connect 设置暂存服务器，并保持最新状态|
||开始将应用从 ADFS 迁移到 Azure AD|
||为领导和日常工作创建自定义仪表板|

### <a name="next-month"></a>下一月

|完成了吗？|项|
|:-|:-|
||监视应用程序的使用情况以深化见解|
||试点启用安全远程访问应用程序|
||确保所有用户都注册了 MFA 和 SSPR|
||启用云身份验证|

### <a name="next-three-months"></a>接下来三个月

|完成了吗？|项|
|:-|:-|
||启用自助服务应用管理|
||启用自助服务组管理|
||监视应用程序的使用情况以深化见解|
||了解支持调用驱动程序|

## <a name="next-steps"></a>后续步骤

了解如何使用 Azure Active Directory 的功能和五步清单（[用于保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)）提升安全性。

了解 Azure AD 的标识功能如何通过提供解决方案和功能来帮助你加快向云控制管理的过渡，并使组织能够快速将更多的标识管理从传统的本地系统中迁移到 Azure AD - [Azure AD 如何为本地工作负载提供云控制管理](./cloud-governed-management-for-on-premises.md)。