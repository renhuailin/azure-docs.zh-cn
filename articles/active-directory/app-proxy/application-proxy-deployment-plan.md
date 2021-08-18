---
title: 计划 Azure Active Directory 应用程序代理部署
description: 有关计划组织内应用程序代理部署的端到端指南
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.openlocfilehash: 3642a48b1b8a5df9a3bc98079873415842b5c1a4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727951"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>计划 Azure AD 应用程序代理部署

Azure Active Directory (Azure AD) 应用程序代理是用于本地应用程序的一种安全且经济高效的远程访问解决方案。 它为“云优先”组织提供即时转换路径来管理对旧版本地应用程序（尚未使用新式协议）的访问。 有关其他介绍性信息，请查看[什么是应用程序代理](./application-proxy.md)。

建议使用应用程序代理为远程用户提供对内部资源的访问权限。 有了应用程序代理，就无需再对这些远程访问用例使用 VPN 或反向代理。 它不适用于企业网络中的用户。 使用应用程序代理进行 Intranet 访问的用户可能会遇到不利的性能问题。

本文提供计划、操作和管理 Azure AD 应用程序代理所需的资源。

## <a name="plan-your-implementation"></a>规划实施

在下列部分，可一目了然地查看将帮助你获取高效部署体验的关键规划要素。

### <a name="prerequisites"></a>先决条件

在开始实施之前，需要先满足以下先决条件。 在本[教程](application-proxy-add-on-premises-application.md)中，可详细了解如何设置环境（包括这些先决条件）。

* **连接器**：连接器是轻量级代理，可部署到以下位置：
   * 本地物理硬件
   * 在任何虚拟机监控程序解决方案中托管的 VM
   * Azure 中托管的用于启用到应用程序代理服务的出站连接的 VM。

* 有关更详细的概述，请查看[了解 Azure AD 应用代理连接器](application-proxy-connectors.md)。

     * 必须先[为 TLS 1.2 启用](application-proxy-add-on-premises-application.md)连接器计算机，然后才能安装连接器。

     * 如果可能，请将连接器部署在与后端 Web 应用程序服务器[相同的网络](application-proxy-network-topology.md)和网段中。 最好是在完成了对应用程序的发现之后再部署连接器。
     * 为了提供高可用性和缩放功能，建议每个连接器组至少有两个连接器。 如果可能随时需要为计算机提供服务，则拥有 3 个连接器是最佳选择。 查看[连接器容量表](./application-proxy-connectors.md#capacity-planning)，帮助确定要在哪种类型的计算机上安装连接器。 计算机越大，连接器的缓冲区就越大，性能也越高。

* **网络访问设置**：Azure AD 应用程序代理连接器 [通过 HTTPS（TCP 端口 443）和 HTTP（TCP 端口 80）连接到 Azure](application-proxy-add-on-premises-application.md)。

   * 不支持终止连接器 TLS 流量，如果终止会使连接器无法与各自的 Azure 应用代理终结点建立安全通道。

   * 避免对连接器与 Azure 之间的出站 TLS 通信进行任何形式的内联检查。 可在连接器与后端应用程序之间进行内部检查，但这可能会降低用户体验，因此不建议这样做。

   * 此外，不支持（甚至不必要）对连接器本身进行负载均衡。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>配置 Azure AD 应用程序代理之前的重要注意事项

必须满足以下核心要求，才能配置和实现 Azure AD 应用程序代理。

*  **Azure 加入**：部署应用程序代理之前，必须从本地目录同步用户标识，或者直接在 Azure AD 租户中创建用户标识。 标识同步允许 Azure AD 在授予用户对应用代理发布的应用程序的访问权限之前对用户进行预身份验证，并获得执行单一登录 (SSO) 所需的用户标识符信息。

* **条件访问要求**：建议不要使用应用程序代理进行 Intranet 访问，因为这会增加延迟，进而影响用户。 建议将应用程序代理与预身份验证和条件访问策略一起使用，以便从 Internet 远程访问。  要对 Intranet 使用提供条件访问，可实现应用程序的现代化，使其能够直接向 AAD 进行身份验证。 有关详细信息，请查看[用于将应用程序迁移到 AAD 的资源](../manage-apps/migration-resources.md)。

* **服务限制**：为了防止各个租户过度消耗资源，对每个应用程序和租户都设置了限制。 若要查看这些限制，请查看 [Azure AD 服务限制和局限性](../enterprise-users/directory-service-limits-restrictions.md)。 这些限制基于远高于典型使用量的基准，为大多数部署提供了充足的缓冲区。

* **公共证书**：如果使用的是自定义域名，则必须购买 TLS/SSL 证书。 根据组织的要求，获取证书可能需要一些时间，建议尽早开始此过程。 Azure 应用程序代理支持标准证书、[通配符](application-proxy-wildcard.md)证书或基于 SAN 的证书。 有关更多详细信息，请查看[使用 Azure AD 应用程序代理配置自定义域](application-proxy-configure-custom-domain.md)。

* **域要求**：若要使用 Kerberos 约束委派 (KCD) 单一登录到已发布的应用程序，运行连接器的服务器和运行应用程序的服务器必须都已加入域，而且属于同一个域或信任域。
有关该主题的详细信息，请查看用于使用应用程序代理[进行单一登录的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)。 连接器服务在本地系统的上下文中运行，不得配置为使用自定义标识。

* **URL 的 DNS 记录**

   * 在应用程序代理中使用自定义域之前，必须先在公共 DNS 中创建一个 CNAME 记录，使客户端能够将自定义的外部 URL 解析为预定义的应用程序代理地址。 如果无法为使用自定义域的应用程序创建 CNAME 记录，将阻止远程用户连接到应用程序。 添加 CNAME 记录所需的步骤可能因 DNS 提供程序而异，因此请了解如何[使用 Azure 门户管理 DNS 记录和记录集](../../dns/dns-operations-recordsets-portal.md)。

   * 同样，连接器主机必须能够解析发布的应用程序的内部 URL。

* **管理权限和角色**

   * 要安装连接器，需要对要在其上安装的 Windows 服务器具有本地管理权限。 它还需要至少有应用程序管理员角色，来进行身份验证并将连接器实例注册到 Azure AD 租户。

   * 要发布和管理应用程序，需要具有应用程序管理员角色。 应用程序管理员可管理目录中的所有应用程序，包括注册、SSO 设置、用户和组的分配与授权、应用程序代理设置以及同意。 它不能授予管理条件访问的能力。 云应用程序管理员角色具有应用程序管理员的所有能力，只是不可管理应用程序代理设置。

* **许可**：可通过 Azure AD Premium 订阅获取应用程序代理。 有关许可选项和功能的完整列表，请查看 [Azure Active Directory 定价页](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

### <a name="application-discovery"></a>应用程序发现

可收集以下信息，就要通过应用程序代理发布的所有范围内的应用程序编译一份清单：

| 信息类型| 要收集的信息 |
|---|---|
| 服务类型| 例如：SharePoint、SAP、CRM、自定义 Web 应用程序、API |
| 应用程序平台 | 例如：Windows IIS、Linux 上的 Apache、Tomcat、NGINX |
| 域成员身份| Web 服务器的完全限定的域名 (FQDN) |
| 应用程序位置 | Web 服务器或场在基础结构中的位置 |
| 内部访问 | 内部访问应用程序时使用的确切 URL。 <br> 如果是场，使用的哪种类型的负载均衡？ <br> 应用程序是否从源（而不是应用本身）中提取内容。<br> 确定应用程序是否通过 Websocket 运行。 |
| 外部访问 | 已在外部向其公开应用程序的供应商解决方案。 <br> 要用于外部访问的 URL。 如果是 SharePoint，请确保根据[本指南](/SharePoint/administration/configure-alternate-access-mappings)配置备用访问映射。 如果不是，则需要定义外部 URL。 |
| 公用证书 | 如果使用自定义域，请购买相应使用者名称的证书。 如果已有证书，请记下序列号和可获取证书的位置。 |
| 身份验证类型| 应用程序支持的身份验证类型，例如基本身份验证、Windows 集成身份验证、基于表单的身份验证、基于标头的身份验证和声明身份验证。 <br>如果将应用程序配置为在特定域帐户下运行，请记下服务帐户的完全限定的域名 (FQDN)。<br> 如果是基于 SAML，需要标识符和回复 URL。 <br> 如果是基于标头，需要供应商解决方案和身份验证类型特定处理要求。 |
| 连接器组名 | 为了向此后端应用程序提供管道和 SSO 而指定的连接器组的逻辑名称。 |
| 用户/组访问权限 | 将获得对应用程序的外部访问权限的用户或用户组。 |
| 其他需求 | 请注意发布应用程序时应考虑的任何其他远程访问或安全要求。 |

可下载此[应用程序清单电子表格](https://aka.ms/appdiscovery)来清点自己的应用。

### <a name="define-organizational-requirements"></a>定义组织要求

应针对以下方面定义组织的业务要求。 每个方面都包含要求示例

 **访问**

* 已加入域的远程用户或已加入 Azure AD 的设备用户可通过无缝单一登录 (SSO) 安全地访问已发布的应用程序。

* 具有获批个人设备的远程用户可安全地访问已发布的应用程序，前提是这些用户已在 MFA 中注册，并且已在其移动电话中注册 Microsoft Authenticator 应用作为一种身份验证方式。

**治理**

* 管理员可定义和监视将用户分配给通过应用代理发布的应用程序的事务的生命周期。

**安全性**

* 只有通过组成员身份或单独分配到应用程序的用户可访问这些应用程序。

**性能**

* 与从内部网络访问应用程序相比，应用程序性能没有下降。

**用户体验**

* 用户了解如何在任何设备平台上使用熟悉的公司 URL 来访问其应用程序。

**审核**
* 管理员可审核用户访问活动。


### <a name="best-practices-for-a-pilot"></a>有关试点的最佳做法

确定完全委派单个应用程序通过单一登录 (SSO) 进行远程访问所需的时间和工作量。 可运行一个涉及其初始发现、发布和常规测试的试点来完成此任务。 使用已针对集成 Windows 身份验证 (IWA) 进行预先配置的简单的基于 IIS 的 Web 应用程序可帮助建立基线，原因是这种设置成功试点远程访问和 SSO 所需的工作量最少。

以下设计元素会提高直接在生产租户中实现试点的成功率。

**连接器管理**：

* 连接器在向应用程序提供本地管道方面发挥着重要的作用。 在将已发布的应用程序委派到生产环境之前，使用默认连接器组足以对它们进行初始试点测试。 然后，已成功测试的应用程序可移动到生产连接器组。

**应用程序管理**：

* 员工最有可能记住的是某个外部 URL 很熟悉而且有关联。 避免使用预定义的 msappproxy.net 或 onmicrosoft.com 后缀发布应用程序。 相反，请提供熟悉的顶级验证域，并以逻辑主机名作为前缀，例如 intranet.<customers_domain>.com。

* 通过在 Azure MyApps 门户中隐藏试点应用程序的启动图标，限制向试点组显示该应用的图标。 准备好用于生产时，可在同一预生产租户中，或者通过在生产租户中发布应用程序，将应用范围限定为对应的目标受众。

**单一登录设置**：某些 SSO 设置具有特定的依赖项，设置它们可能需要一些时间，因此请确保提前处理好依赖项，避免变更控制延迟。 这包括域加入连接器主机来使用 Kerberos 约束委派 (KCD) 执行 SSO，以及处理其他耗时的活动。 例如，设置 PING 访问实例（如果需要基于标头的 SSO）。

**连接器主机和目标应用程序之间的 TLS**：安全性非常重要，因此应始终使用连接器主机和目标应用程序之间的 TLS。 特别是如果为基于表单的身份验证 (FBA) 配置 Web 应用程序，则会以明文形式有效地传输用户凭据。

**增量实现并测试每个步骤**。
在发布应用程序后执行基本的功能测试，来确保满足所有用户和业务要求，方法如下：

1. 测试和验证对已禁用预身份验证的 Web 应用程序的常规访问。
2. 如果成功，则启用预身份验证并分配用户和组。 测试和验证访问权限。
3. 然后，添加应用程序的 SSO 方法，并再次测试来验证访问权限。
4. 根据需要应用条件访问和 MFA 策略。 测试和验证访问权限。

**故障排除工具**：进行故障排除时，始终先从连接器主机上的浏览器验证对已发布的应用程序的访问，再确认应用程序是否按预期方式工作。 设置越简单，就越容易确定根本原因，因此请考虑尝试使用最小配置（例如仅使用单个连接器且不使用 SSO）重现问题。 在某些情况下，Web 调试工具（如 Telerik 的 Fiddler）在通过代理访问的应用程序中排查访问或内容问题时是不可或缺的。 Fiddler 还可充当代理来帮助跟踪和调试移动平台（如 iOS 和 Android）的流量，以及可配置为通过代理进行路由的几乎所有内容。 有关详细信息，请查看[故障排除指南](application-proxy-troubleshoot.md)。

## <a name="implement-your-solution"></a>实施解决方案

### <a name="deploy-application-proxy"></a>部署应用程序代理

如需部署应用程序代理的步骤，可查看[教程：添加本地应用程序进行远程访问](application-proxy-add-on-premises-application.md)。 如果安装失败，请在门户中选择“应用程序代理故障排除”，或者按照[安装应用程序代理连接器时遇到的问题](application-proxy-connector-installation-problem.md)这一故障排除指南进行操作。

### <a name="publish-applications-via-application-proxy"></a>通过应用程序代理发布应用程序

发布应用程序时，假设你已满足所有先决条件，并且你有多个连接器在“应用程序代理”页面中显示为已注册且有效。

还可使用 [PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 来发布应用程序。

下面是发布应用程序时应遵循的一些最佳做法：

* **使用连接器组**：分配已指定用于发布每个相应的应用程序的连接器组。 为了提供高可用性和缩放功能，建议每个连接器组至少有两个连接器。 如果可能随时需要为计算机提供服务，则拥有 3 个连接器是最佳选择。 此外，请查看[使用连接器组在单独的网络和位置中发布应用程序](application-proxy-connector-groups.md)，了解还能如何使用连接器组按网络或位置将连接器分段。

* **设置后端应用程序超时**：如果应用程序处理客户端事务时可能耗时超过 75 秒，那么此设置非常有用。 例如，如果客户端将查询发送到充当数据库前端的 Web 应用程序。 前端将此查询发送到其后端数据库服务器并等待响应，但在收到响应时，会话的客户端超时。将“超时”设置为“长”可提供 180 秒的时间来完成较长的事务。

* **使用适当的 Cookie 类型**

   * **仅限 HTTP 的 Cookie**：通过让应用程序代理在 set-cookie HTTP 响应头中包含 HTTPOnly 标志来增强安全性。 此设置有助于缓解跨站脚本 (XSS) 等攻击。 对于需要访问会话 Cookie 的客户端/用户代理，请将此设置保留为“否”。 例如，连接到通过应用代理发布的远程桌面网关的 RDP/MTSC 客户端。

   * **安全 Cookie**：使用 Secure 属性设置 Cookie 时，如果通过 TLS 安全通道传输请求，则用户代理（客户端应用）将仅在 HTTP 请求中包含 Cookie。 这有助于降低 Cookie 在明文通道上泄露的风险，因此应启用此项。

   * **永久性 Cookie**：允许应用程序代理会话 Cookie 在浏览器关闭期间持续有效，直到它过期或被删除。 适用于富应用程序（如 Office）访问已发布的 Web 应用程序中的文档的场景，无需重新提示用户进行身份验证。 启用时务必小心，因为如果永久性 Cookie 不与其他补偿控制结合使用，永久性 Cookie 最终会使服务面临未经授权的访问的风险。 此设置应仅用于无法在进程之间共享 Cookie 的较旧的应用程序。 更好的做法是更新应用程序，以在进程之间共享 Cookie，而不是使用此设置。

* **转换标头中的 URL**：在不能将内部 DNS 配置为与组织的公共命名空间（也称为）相匹配的场景中，可启用此设置。 除非应用程序需要客户端请求中的原始主机头，否则请将此值设置为“是”。 另一种方法是让连接器使用内部 URL 中的 FQDN 来路由实际流量，使用外部 URL 中的 FQDN 作为主机头。 在大多数情况下，这种替代方法应允许应用程序在远程访问时正常工作，但用户不再拥有匹配的内部和外 URL。

* **转换应用程序主体中的 URL**：如果想要将应用中的链接在响应中转换回客户端，请打开该应用的应用程序主体链接转换。 启用后，此功能会尽量转换应用代理在 HTML 和返回到客户端的 CSS 响应中找到的所有内部链接。 如果发布在内容中包含硬编码的绝对或 NetBIOS 短名称链接的应用，或者包含链接到其他本地应用程序的内容的应用，那么此功能非常有用。

对于已发布的应用链接到其他已发布的应用的情况，请为每个应用程序都启用链接转换，以便可在每个应用级别控制用户体验。

例如，假设有 3 个应用程序（Benefits、Expenses 和 Travel）通过应用程序代理发布，它们彼此链接，还有第 4 个应用 Feedback，它不是通过应用程序代理发布的。

![图 1](media/App-proxy-deployment-plan/link-translation.png)

如果为 Benefits 应用启用链接转换，到 Expenses 和 Travel 的链接将重定向到这些应用的外部 URL，因此从企业网络外部访问应用程序的用户可访问它们。 由于这两个应用尚未启用链接转换，因此从 Expenses 和 Travel 转回 Benefits 的链接不起作用。 不会重定向到 Feedback 的链接，原因是没有外部 URL，因此使用 Benefits 应用的用户将无法从企业网络外部访问 Feedback 应用。 有关详细信息，请查看[链接转换和其他重定向选项](application-proxy-configure-hard-coded-link-translation.md)。

### <a name="access-your-application"></a>访问应用程序

有多个选项可用于管理对应用代理发布的资源的访问，因此请根据给定方案和伸缩性需求选择最适合的选项。 常见的方法包括：使用正在通过 Azure AD Connect 同步的本地组、根据用户属性在 Azure AD 中创建动态组、使用资源所有者管理的自助服务组，或者结合使用这三种方法。 请查看链接的资源，了解每种方法的优点。

要向用户分配访问应用程序的权限，最直接的方法是在转到已发布的应用程序的左窗格中的“用户和组”选项，直接分配组或个人。

![图片 24](media/App-proxy-deployment-plan/add-user.png)

还可分配用户当前不属于的组，并配置自助选项，来允许用户自助访问应用程序。

![图 25](media/App-proxy-deployment-plan/allow-access.png)

如果启用，用户将能够登录 MyApps 门户并请求访问权限；系统可能会自动批准他们并将其添加到已允许的自助服务组，或者要求指定的审批者对其进行审批。

来宾用户也可[在 AZURE AD B2B 中受邀访问通过应用程序代理发布的内部应用程序](../external-identities/add-users-information-worker.md)。

对于通常可匿名访问的本地应用程序，不需要进行身份验证，而你可能更希望禁用应用程序“属性”中的选项。

![图 26](media/App-proxy-deployment-plan/assignment-required.png)


如果将此选项的设置保留为“否”，那么用户无需权限即可通过 Azure AD 应用代理访问本地应用程序，因此请谨慎使用此选项。

发布应用程序后，应该可通过在浏览器中键入其外部 URL，或在 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 上通过其图标来访问他。

### <a name="enable-pre-authentication"></a>启用预身份验证

验证是否可通过应用程序代理利用外部 URL 访问应用程序。

1. 导航到“Azure Active Directory” > “企业应用程序” > “所有应用程序”，选择要管理的应用。

2. 选择“应用程序代理”。

3. 在“预身份验证”字段中，使用下拉列表选择“Azure Active Directory”，然后选择“保存”  。

启用预身份验证后，Azure AD 将首先质询用户来进行身份验证，如果配置了单一登录，则后端应用程序也会在授予应用程序访问权限之前验证用户。 如果将预身份验证模式从“直通”改为“Azure AD”，会通过 HTTPS 配置外部 URL，因此最初为 HTTP 配置的所有应用程序现在都将通过 HTTPS 进行保护。

### <a name="enable-single-sign-on"></a>启用单一登录

SSO 可提供最佳的用户体验和安全性，这是因为用户在访问 Azure AD 时只需登录一次。 用户经过预身份验证后，应用程序代理连接器会代表用户向本地应用程序进行身份验证来执行 SSO。 后端应用程序处理登录，就像它是用户本身一样。

如果选择“直通”选项，用户可访问已发布的应用程序，而无需向 Azure AD 进行身份验证。

仅当 Azure AD 可识别请求访问资源的用户时，才可执行 SSO，因此必须将应用程序配置为在访问时使用 Azure AD 对用户进行预身份验证，这样 SSO 才能正常工作，否则将禁用 SSO 选项。

请阅读[在 Azure AD 中单一登录到应用程序](../manage-apps/what-is-single-sign-on.md)，它可帮助你在配置应用程序时选择最适合的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他类型的应用程序

Azure AD 应用程序代理还支持为使用 [Microsoft 身份验证库 (MSAL)](../develop/v2-overview.md) 开发的应用程序。 它通过使用在客户端请求的标头信息中收到的 Azure AD 颁发的令牌来代表用户执行预身份验证，从而支持本机客户端应用。

若要了解应用程序代理的可用配置，请阅读[发布本机和移动客户端应用](./application-proxy-configure-native-client-application.md)和[基于声明的应用程序](./application-proxy-configure-for-claims-aware-applications.md)。

### <a name="use-conditional-access-to-strengthen-security"></a>使用条件访问增强安全性

应用程序安全性要求使用一组可防御和应对本地和云中复杂威胁的高级安全功能。 攻击者通常通过薄弱、默认或盗用的用户凭据来获取企业网络访问权限。  Microsoft 标识驱动的安全性通过管理和保护特权和非特权标识来减少对盗用的凭据的使用。

可使用以下功能来支持 Azure AD 应用程序代理：

* 基于用户和位置的条件访问：使用[基于位置的条件访问策略](../conditional-access/location-condition.md)限制基于地理位置或 IP 地址的用户访问来保护敏感数据。

* 基于设备的条件访问：确保只有已注册、已批准和合规的设备才能使用[基于设备的条件访问](../conditional-access/require-managed-devices.md)来访问公司数据。

* 基于应用程序的条件访问：如果用户不在企业网络上，则不需要停止工作。 [保护对企业云和本地应用的访问](../conditional-access/app-based-conditional-access.md)，并使用条件访问保持控制。

* 基于风险的条件访问：使用[基于风险的条件访问策略](https://www.microsoft.com/cloud-platform/conditional-access)保护数据免受恶意黑客的攻击；无论在本地还是在云中，该策略都可应用于所有应用和所有用户。

* Azure AD 的“我的应用”：部署应用程序代理服务和安全发布应用程序后，为用户提供一个简单的中心用来发现和访问其所有应用程序。 通过[我的应用](https://aka.ms/AccessPanelDPDownload)利用自助服务功能提高工作效率，这些功能包括可请求访问新应用和组，或者可代表其他人管理对这些资源的访问。

## <a name="manage-your-implementation"></a>管理实施

### <a name="required-roles"></a>必需的角色

Microsoft 倡导的原则是，使用 Azure AD 为执行必需任务授予尽量最小的特权。 [查看不同可用的 Azure 角色](../roles/permissions-reference.md)，并选择相应的角色来满足各职位的需求。 可能需要暂时应用某些角色，在部署完成后再将其删除。

| 公司角色| 公司任务| Azure AD 角色 |
|---|---|---|
| 咨询台管理员 | 通常限制为限定最终用户报告的问题和执行有限的任务（例如更改用户的密码、使刷新令牌失效以及监视服务运行状况）。 | 支持管理员 |
| 标识管理员| 阅读 Azure AD 登录报表和审核日志来调试与应用代理相关的问题。| 安全读取者 |
| 应用程序所有者| 创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。| 应用程序管理员 |
| 基础结构管理员 | 证书滚动更新所有者 | 应用程序管理员 |

尽量减少拥有访问权限的人员以保护信息或资源安全，这样可以减少恶意行动者获得未授权访问权限，或者已授权用户无意中影响敏感资源的可能性。

但是，用户仍需执行日常特权操作，因此请强制执行基于实时 (JIT) 的 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 策略，来提供对 Azure 资源的按需特权访问权限，建议使用 Azure AD 对管理访问权限和审核进行有效地管理。

### <a name="reporting-and-monitoring"></a>报告和监视

Azure AD 通过[审核日志和报告](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)更深入地显示组织的应用程序使用情况和操作运行状况。 此外，可通过应用程序代理轻松地从 Azure AD 门户和 Windows 事件日志监视连接器。

#### <a name="application-audit-logs"></a>应用程序审核日志

在这些日志中，可详细了解对配置了应用程序代理的应用程序的登录情况，以及访问该应用程序的设备和用户。 [审核日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)可导出，它们位于 Azure 门户和[审核 API](/graph/api/resources/directoryaudit)中。 此外，还向应用程序提供[使用情况和见解报表](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

#### <a name="application-proxy-connector-monitoring"></a>应用程序代理连接器监视

连接器和服务负责处理所有的高可用性任务。 可通过 Azure AD 门户中的“应用程序代理”页面监视连接器的状态。 有关连接器维护的详细信息，请查看[了解 Azure AD 应用程序代理连接器](./application-proxy-connectors.md#maintenance)。

![示例：Azure AD 应用程序代理连接器](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件日志和性能计数器

连接器提供管理日志和会话日志。 管理日志包括关键事件及其错误。 会话日志包括所有事务及其处理详细信息。 可在 Windows 事件日志中找到日志和计数器。有关详细信息，请查看[了解 Azure AD 应用程序代理连接器](./application-proxy-connectors.md#under-the-hood)。 请按照[教程：在 Azure Monitor 中配置事件日志数据源](../../azure-monitor/agents/data-sources-windows-events.md)进行操作。

### <a name="troubleshooting-guide-and-steps"></a>故障排除指南和步骤

通过[故障排除](application-proxy-troubleshoot.md)错误消息指南，详细了解常见问题及解决方法。

以下文章介绍了一些常见方案，它们也可用于为支持组织创建故障排除指南。

* [显示应用页时出现问题](application-proxy-page-appearance-broken-problem.md)
* [应用程序加载时间过长](application-proxy-page-load-speed-problem.md)
* [应用程序页上的链接不起作用](application-proxy-page-links-broken-problem.md)
* [要为应用打开哪些端口](application-proxy-add-on-premises-application.md)
* [应用的连接器组中没有正常工作的连接器](application-proxy-connectivity-no-working-connector.md)
* [在管理门户中配置](application-proxy-config-how-to.md)
* [为应用配置单一登录](application-proxy-config-sso-how-to.md)
* [在管理门户中创建应用时出现问题](application-proxy-config-problem.md)
* [配置 Kerberos 约束委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用 PingAccess 配置](application-proxy-ping-access-publishing-guide.md)
* [“无法访问此企业应用程序”错误](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [安装应用程序代理程序连接器时出现问题](application-proxy-connector-installation-problem.md)
* [登录问题](application-sign-in-problem-on-premises-application-proxy.md)
