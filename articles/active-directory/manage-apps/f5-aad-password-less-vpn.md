---
title: Azure AD 使用 F5 VPN 保护混合访问 |Microsoft Docs
description: Azure Active Directory 单一登录 (SSO) 与用于无密码 VPN 的 F5 大 IP 集成的教程
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730881"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>有关采用无密码 VPN 的 F5 大 IP Azure Active Directory 单一登录集成的教程

在本教程中，了解如何将 F5 bigip 的基于大 IP 的安全套接字层虚拟专用网络 (SSL VPN) 解决方案与 Azure Active Directory (AD) 集成， (SHA) 。

将 IP SSL 的大 VPN 与 Azure AD 集成提供了 [许多重要优势](f5-aad-integration.md)，包括：

- 通过[Azure AD 预身份验证和授权](../../app-service/overview-authentication-authorization.md)提高了零信任管理

- [对 VPN 服务的无密码身份验证](https://www.microsoft.com/security/business/identity/passwordless)

- 从单个控制平面管理标识和访问- [Azure 门户](https://portal.azure.com/#home)

尽管这些极佳的价值增加，但经典 VPN 仍会依据网络外围的概念，其中受信任的是内部的，不受信任。 由于公司资产不再局限于企业数据中心的墙纸，而是在无固定边界的多云环境中，因此此模型不再有效。 出于此原因，我们鼓励客户考虑在 [按应用程序管理访问权限](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)时，转向更多标识驱动的方法。

## <a name="scenario-description"></a>方案描述

在此方案中，SSL VPN 服务的大 IP APM 实例将配置为 SAML 服务提供程序 (SP) 并 Azure AD 成为受信任的 SAML IDP，从而提供预身份验证。 通过基于声明的身份验证向大 IP APM 提供单一登录 (SSO Azure AD) ，提供无缝的 VPN 访问体验。

![图像显示 ssl-vpn 体系结构](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>本指南中引用的所有示例字符串或值都应该替换为实际环境的所有示例。

## <a name="prerequisites"></a>先决条件

如果事先了解并不需要对大 IP IP 进行了解，则需要：

- Azure AD [免费订阅](https://azure.microsoft.com/trial/get-started-active-directory/) 或更高版本

- 应将用户标识 [从其本地目录同步](../hybrid/how-to-connect-sync-whatis.md) 到 Azure AD。

- 具有 Azure AD 的应用程序管理员[权限](../roles/permissions-reference.md#application-administrator)的帐户

- 一个现有的大 IP 基础结构，其中包含进出大 IP 的客户端流量，或将 [大 Ip 虚拟版部署到 Azure](f5-bigip-deployment-guide.md)中。

- 大 IP 发布的 VPN 服务的记录将需要在公共 DNS 中存在，或在测试时位于测试客户端的 localhost 文件中

- 应将大 IP 设置为通过 HTTPS 发布服务所需的 SSL 证书。

通过 [F5 大 IP 术语](https://www.f5.com/services/resources/glossary) 熟悉，还可以帮助了解整个教程中所引用的各种组件。

>[!NOTE]
>如果你发现本指南中的说明与 Azure 门户中显示的内容有任何差别，Azure 会不断演变。 从大 IP v15 开始，屏幕快照与 v 13.1 之间保持相对相似。

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>从 Azure AD 库添加 F5 大 IP

设置大 IP 之间的 SAML 联合身份验证信任允许 Azure AD 大 IP 在授予对发布的 VPN 服务的访问权限之前，将预身份验证和 [条件访问](../conditional-access/overview.md) 移交给 Azure AD。

1. 使用具有应用程序管理员权限的帐户登录到 Azure AD 门户

2. 从左侧导航窗格中，选择 **Azure Active Directory 服务**

3. 从顶级功能区中转到 " **企业应用程序** "，选择 " **新建应用程序**"。

4. 在库中搜索 F5，然后选择 **F5 大 IP APM Azure AD 集成**。

5. 提供应用程序的名称，然后提供 " **添加/创建** "，将其添加到租户。 用户可以在 Azure 和 Office 365 应用程序门户中看到名称作为图标。 该名称应反映该特定服务。 例如，VPN。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

1. 在视图中查看新的 F5 应用程序属性，请参阅 **管理**  >  **单一登录**

2. 在“选择单一登录方法”页上选择“SAML” 。 通过选择 "否" 跳过保存单一登录设置的提示 **，稍后保存**。

3. 在 " **设置 SAML 的单一登录** " 菜单上，选择 " **基本 SAML 配置** " 的 "笔" 图标以提供以下详细信息：

   - 将预定义的 **标识符 url** 替换为大 IP 发布服务的 url。 例如： `https://ssl-vpn.contoso.com`

   - 对 " **回复 URL** " 文本框执行相同操作，包括 SAML 终结点路径。 例如： `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - 在此配置中，应用程序将在 IDP 发起的模式下运行，其中 Azure AD 在重定向到大 IP SAML 服务之前，使用 SAML 断言颁发用户。 对于不支持 IDP 启动模式的应用，请指定大 IP SAML 服务的 **登录 URL** 。 例如，`https://ssl-vpn.contoso.com`。

   - 对于 "注销 Url"，输入要发布的服务的主机标头预先挂起的大 IP APM 单一注销 (SLO) 终结点。 例如： `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   提供 SLO URL 可确保用户在用户注销后，在两个端（即大 IP 和 Azure AD）终止用户会话。大 IP APM 还提供了一个 [选项](https://support.f5.com/csp/article/K12056) ，用于在调用特定的应用程序 URL 时终止所有会话。

![图像显示基本 saml 配置](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>从 TMOS v16 中，SAML SLO 终结点已更改为/saml/sp/profile/redirect/slo

4. 在退出 SAML 配置菜单之前选择 " **保存** "，并跳过 SSO 测试提示。

观察 **用户属性 & 声明** "部分的属性，因为 Azure AD 会向用户颁发大 IP APM 身份验证。

![图像显示用户属性声明](media/f5-sso-vpn/user-attributes-claims.png)

可以随意添加大 IP 已发布服务所需的任何其他特定声明，同时请注意，除默认集外定义的任何声明都将仅在 Azure AD 中存在，作为填充的属性。 同样，目录 [角色或组](../hybrid/how-to-connect-fed-group-claims.md) 成员身份还需要针对 Azure AD 中的用户对象进行定义，然后才能将其作为声明发出。

![图像显示联合元数据下载链接](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD 创建的 SAML 签名证书的生命周期为三年，因此将需要使用 Azure AD 发布的指南进行管理。

### <a name="azure-ad-authorization"></a>Azure AD 授权

默认情况下，Azure AD 仅向已获授权访问服务的用户颁发令牌。

1. 仍在应用程序的 "配置" 视图中，选择 "**用户和组**"

2. 选择 " **+ 添加用户**"，然后在 "添加分配" 菜单中选择 "**用户和组**"

3. 在 "**用户和组**" 对话框中，添加有权访问 VPN 的用户组，然后 **选择**"  >  **分配**"

![图像显示添加用户链接 ](media/f5-sso-vpn/add-user-link.png)

4. 这将完成 SAML 联合身份验证信任的 Azure AD 部分。 现在，可将大 IP APM 设置为发布 SSL VPN 服务，并使用相应的属性集来完成 SAML 预身份验证的配置。

## <a name="big-ip-apm-configuration"></a>大 IP APM 配置

### <a name="saml-federation"></a>SAML 联合

以下部分创建完成 Azure AD 的 VPN 服务联合所需的大 IP SAML 服务提供程序和相应的 SAML IDP 对象。

1. 请参阅 **访问**  >  **联合身份验证**  >  **SAML 服务提供商**  >  **本地 SP 服务** 并选择 **创建**

![图像显示大 IP SAML 配置](media/f5-sso-vpn/bigip-saml-configuration.png)

2. 输入在前面 Azure AD 中定义的 " **名称** " 和 " **实体 ID** " 值，并输入将用于连接到应用程序的主机 FQDN

![图像显示了如何创建新的 SAML SP 服务](media/f5-sso-vpn/create-new-saml-sp.png)

   仅当实体 ID 不是已发布 URL 的主机名部分的完全匹配时，或者它不是基于主机名的 URL 格式时，才需要 SP **名称** 设置。 如果实体 ID 为，请提供要发布的应用程序的外部方案和主机名 `urn:ssl-vpn:contosoonline` 。

3. 向下滚动以选择新的 **SAML SP 对象** ，并选择 " **绑定/取消绑定 IDP 连接器**"。

![图像显示了如何创建与本地 SP 服务的联合](media/f5-sso-vpn/federation-local-sp-service.png)

4. 选择 "**新建 IDP 连接器**"，然后从下拉菜单中选择 "**从元数据**"

![Image 显示创建新的 IDP 连接器](media/f5-sso-vpn/create-new-idp-connector.png)

5. 浏览到之前下载的联合元数据 XML 文件，并为将代表外部 SAML IDP 的 APM 对象提供 **标识提供程序名称** 。

6. 选择 " **添加新行** " 以选择新的 AZURE AD external IDP 连接器。

![Image 显示外部 IDP 连接器](media/f5-sso-vpn/external-idp-connector.png)

7. 选择 " **更新** "，将 saml SP 对象绑定到 saml IDP 对象，然后选择 **"确定"**。

![使用 SP 显示 SAML IDP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Webtop 配置

以下步骤启用通过大 IP 的专有 web 门户向用户提供 SSL VPN。

1. 请 **访问**  >  **Webtops**  >  **Webtop 列表**，并选择 "**创建**"。

2. 为门户指定名称并将类型设置为 " **完整**"。 例如，`Contoso_webtop`。

3. 调整其余首选项，然后选择 " **完成**"。

![图像显示 webtop 配置](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN 配置

VPN 功能由多个元素组成，每个元素控制整个服务的不同方面。

1. 请参阅 **访问**  >  **连接性/vpn**  >  **网络访问 (VPN)**  >  **IPV4 租用池**，然后选择 "**创建**"。

2. 提供要分配给 VPN 客户端的 IP 地址池的名称。 例如，Contoso_vpn_pool

3. 将 "类型" 设置为 " **Ip 地址范围** "，然后提供 "开始" 和 "结束" ip，然后 **添加** 和 " **完成**"

![图像显示 vpn 配置](media/f5-sso-vpn/vpn-configuration.png)

网络访问列表使用 VPN 池中的 IP 和 DNS 设置以及用户路由权限来设置服务，如果需要，还可以启动应用程序。

1. 请参阅 **访问**  >  **连接性/vpn：网络访问 (VPN)**  >  **网络访问列表**，然后选择 "**创建**"。

2. 为 VPN 访问列表和标题提供一个名称，例如，Contoso-VPN 后跟 **已完成**。

![图像在网络访问列表中显示 vpn 配置](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. 在顶部功能区中，选择 " **网络设置** "，并添加以下设置：

   • **支持的 IP 版本**： IPV4

   • **IPV4 租赁池**：选择前面创建的 VPN 池，例如 Contoso_vpn_pool

![图像显示 contoso vpn 池](media/f5-sso-vpn/contoso-vpn-pool.png)

   客户端设置选项可用于强制限制在建立 VPN 时如何路由客户端流量。

4. 选择 " **已完成** "，并中转到 "DNS/主机" 选项卡以添加设置：

   • **IPV4 主名称服务器**：环境的 DNS 服务器的 IP

   • **DNS 默认域后缀**：此特定 VPN 连接的域后缀。 例如，contoso.com

![Image 显示默认域后缀](media/f5-sso-vpn/domain-suffix.png)

[F5 文章](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) 提供了有关根据您的偏好调整剩余设置的详细信息。

现在需要使用大 IP 连接配置文件来为 VPN 服务需要支持的每个 VPN 客户端类型配置设置。 例如，"Windows"、"OSX" 和 "Android"。

1. 请 **访问访问**  >  **连接/VPN**  >  **连接**  >  **配置文件**，并选择 "**添加**"。

2. 提供配置文件名称并将父配置文件设置为 **/Common/connectivity**，例如 Contoso_VPN_Profile。

![图像显示 "创建新的连接配置文件"](media/f5-sso-vpn/create-connectivity-profile.png)

F5 bigip [文档](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) 提供有关客户端支持的更多详细信息。

## <a name="access-profile-configuration"></a>访问配置文件配置

配置 VPN 对象后，需要使用访问策略来启用 SAML 身份验证服务。

1. 请访问 **访问**  >  **配置文件/策略**  >  **访问配置文件 (按会话策略)** 并选择 "**创建**"

2. 提供配置文件名称，为配置文件类型选择 " **全部**"，例如 Contoso_network_access

3. 向下滚动以将至少一种语言添加到 "**接受语言**" 列表中，然后选择 "**完成**"

![图像显示常规属性](media/f5-sso-vpn/general-properties.png)

4. 选择新访问配置文件的 "Per-Session 策略" 字段中的 " **编辑** "，以便在单独的浏览器选项卡中启动 "视觉策略编辑器"。

![图像显示每个会话的策略](media/f5-sso-vpn/per-session-policy.png)

5. 选择 " **+** 登录"，然后在弹出窗口中选择 "**身份验证**  >  **SAML 身份** 验证  >  **添加项**"。

6. 在 "SAML 身份验证 SP 配置" 中，选择之前创建的 VPN SAML SP 对象，然后选择 " **保存**"。

![图像显示 saml 身份验证](media/f5-sso-vpn/saml-authentication.png)

7. **+** 为 SAML 身份验证的成功分支选择。

8. 在 "分配" 选项卡中，依次选择 "**高级资源分配**" 和 "**添加项**"

![图像显示高级资源分配](media/f5-sso-vpn/advance-resource-assign.png)

9. 在弹出窗口中，选择 " **新建项** "，然后选择 " **添加/删除**"。

10. 在子窗口中，选择 " **网络访问** "，然后选择前面创建的网络访问配置文件

![图像显示添加新的网络访问条目](media/f5-sso-vpn/add-new-entry.png)

11. 切换到 **webtop** 选项卡并添加之前创建的 webtop 对象。

![图像显示添加 webtop 对象](media/f5-sso-vpn/add-webtop-object.png)

12. 选择 " **更新** "，然后选择 " **保存**"。

13. 选择上一个 "拒绝" 框中的链接，以更改成功的分支以 **允许** 然后 **保存**。

![图像显示新的视觉策略编辑器](media/f5-sso-vpn/vizual-policy-editor.png)

14. 选择 " **应用访问策略** " 并关闭 "视觉策略编辑器" 选项卡，提交这些设置。

![图像显示新的访问策略管理器](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>发布 VPN 服务

设置好所有设置后，APM 现在需要一个前端虚拟服务器来侦听连接到 VPN 的客户端。

1. 选择 "**本地流量**  >  **虚拟服务器**"  >  **虚拟服务器列表**，然后选择 "**创建**"。

2. 提供 VPN 虚拟服务器的 **名称** ，例如 **VPN_Listener**。

3. 为虚拟服务器提供一个未使用的 **IP 目标地址** ，该地址具有用于接收客户端流量的路由

4. 将服务端口设置为 **443 HTTPS** ，并确保状态显示为 "**已启用**"

![图像显示新的虚拟服务器](media/f5-sso-vpn/new-virtual-server.png)

5. 将 **Http 配置文件** 设置为 http，并添加 SSL 配置文件 (客户端) 作为必备组件的一部分预配的公用 SSL 证书。

![图像显示 ssl 配置文件](media/f5-sso-vpn/ssl-profile.png)

6. 在 "访问策略" 下，将 " **访问配置文件** 和 **连接配置文件** " 设置为使用创建的 VPN 对象。

![图像显示访问策略](media/f5-sso-vpn/access-policy.png)

7. 完成后选择 " **完成** "。

8.  SSL VPN 服务现在可以通过 SHA 直接通过其 URL 或通过 Microsoft 的应用程序门户进行发布和访问。

## <a name="additional-resources"></a>其他资源

- [密码结束，请无密码](https://www.microsoft.com/security/business/identity/passwordless)

- [什么是条件访问？](../conditional-access/overview.md)

- [用于启用远程工作的 Microsoft 零信任框架](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [与 Azure AD 完全应用程序集成的五个步骤](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>后续步骤

在远程 Windows 客户端上打开浏览器，并浏览到 **大 IP VPN 服务** 的 url。 向 Azure AD 进行身份验证后，会看到大 IP webtop 门户和 VPN 启动器。

![图像显示 vpn 启动器](media/f5-sso-vpn/vpn-launcher.png)

选择 VPN 磁贴将安装大 IP 边缘客户端，并建立为 SHA 配置的 VPN 连接。
F5 VPN 应用程序还应在 Azure AD 条件访问中作为目标资源可见。 请参阅我们的 [指南](../conditional-access/concept-conditional-access-policies.md) ，了解如何构建条件性访问策略，以及如何使用户能够 Azure AD 无 [密码身份验证](https://www.microsoft.com/security/business/identity/passwordless)。