---
title: 使用 F5 VPN 实现 Azure AD 安全混合访问 | Microsoft Docs
description: 关于将 Azure Active Directory 单一登录 (SSO) 与 F5 BIG-IP 集成以实现无密码 VPN 的教程
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: 34175414cd5203438de76a94de7896e3bf175355
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744165"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>关于将 Azure Active Directory 单一登录与 F5 BIG-IP 集成以实现无密码 VPN 的教程

在本教程中，了解如何将 F5 基于 BIG-IP 的安全套接字层虚拟专用网 (SSL-VPN) 解决方案与 Azure Active Directory (AD) 集成，以实现安全混合访问 (SHA)。

将 BIG-IP SSL-VPN 与 Azure AD 集成可提供[许多重要优势](f5-aad-integration.md)，其中包括：

- 通过 [Azure AD 预身份验证和授权](../../app-service/overview-authentication-authorization.md)实现的零信任治理改进

- [对 VPN 服务的无密码身份验证](https://www.microsoft.com/security/business/identity/passwordless)

- 从单个控制平面（[Azure 门户](https://portal.azure.com/#home)）管理标识和访问

尽管有这些极佳的附加价值，但经典 VPN 仍然基于网络外围的概念，其中内部受到信任，外部不受信任。 此模型在实现真正的零信任态势方面不再有效，因为公司资产不再局限于企业数据中心的围墙之内，而是跨越没有固定边界的多云环境。 因此，我们鼓励客户在[基于每个应用程序管理访问权限](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)时考虑转为更偏向于标识驱动的方法。

## <a name="scenario-description"></a>方案描述

在此方案中，SSL-VPN 服务的 BIG-IP APM 实例将配置为 SAML 服务提供程序 (SP) 且 Azure AD 会成为受信任的 SAML IDP，从而提供预身份验证。 然后，通过基于声明的身份验证向 BIG-IP APM 提供来自 Azure AD 的单一登录 (SSO)，从而提供无缝的 VPN 访问体验。

![该图像显示了 SSL-VPN 体系结构](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>本指南中引用的所有示例字符串或值都应替换为实际环境中使用的字符串或值。

## <a name="prerequisites"></a>先决条件

不要求以前必须拥有 F5 BIG-IP 经验或知识，但需要满足以下要求：

- 拥有 Azure AD [免费订阅](https://azure.microsoft.com/trial/get-started-active-directory/)或更高版本

- 用户标识应[从其本地目录同步](../hybrid/how-to-connect-sync-whatis.md)到 Azure AD。

- 帐户具有 Azure AD 应用程序管理员[权限](../roles/permissions-reference.md#application-administrator)

- 现有 BIG-IP 基础结构具有往返 BIG-IP 的客户端流量路由，或[将 BIG-IP 虚拟版部署到 Azure](f5-bigip-deployment-guide.md)。

- 在测试过程中，公共 DNS 或测试客户端的 localhost 文件中必须存在 BIG-IP 发布的 VPN 服务的记录

- BIG-IP 应预配通过 HTTPS 发布服务所需的 SSL 证书。

熟悉 [F5 BIG-IP 术语](https://www.f5.com/services/resources/glossary)也将有助于理解本教程中引用的各种组件。

>[!NOTE]
>Azure 在不断演变，因此，如果发现本指南中的说明与 Azure 门户中看到的内容之间有任何细微差别，不要感到奇怪。 屏幕截图来自 BIG-IP v15，但与 v13.1 仍然相对相似。

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>从 Azure AD 库添加 F5 BIG-IP

在 BIG-IP 之间设置 SAML 联合身份验证信任让 Azure AD BIG-IP 能够在授予对已发布 VPN 服务的访问权限之前，将预身份验证和[条件访问](../conditional-access/overview.md)转交给 Azure AD。

1. 使用具有应用程序管理员权限的帐户登录到 Azure AD 门户

2. 在左侧导航窗格中，选择“Azure Active Directory 服务”

3. 转到“企业应用程序”，然后从顶部功能区中选择“新建应用程序”。

4. 在库中搜索 F5，然后选择“F5 BIG-IP APM Azure AD 集成”。

5. 为应用程序提供名称，然后选择“添加/创建”以将其添加到租户中。 用户可以在 Azure 和 Office 365 应用程序门户中看到名称以图标形式显示。 名称应反映该特定服务。 例如，VPN。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

1. 视图中出现新 F5 应用程序的属性后，转到“管理” > “单一登录”

2. 在“选择单一登录方法”页上选择“SAML” 。 选择“否，稍后保存”，跳过保存单一登录设置的提示。

3. 在“设置 SAML 单一登录”菜单上，选择“基本 SAML 配置”的笔形图标以提供以下详细信息：

   - 将预定义的“标识符 URL”替换为 BIG-IP 已发布服务的 URL。 例如，`https://ssl-vpn.contoso.com`

   - 对“回复 URL”文本框（包括 SAML 终结点路径）执行相同的操作。 例如，`https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - 仅在此配置中，应用程序会在 IDP 发起的模式下运行，在该模式下，Azure AD 会先向用户颁发 SAML 断言，然后再重定向到 BIG-IP SAML 服务。 对于不支持 IDP 发起模式的应用，请为 BIG-IP SAML 服务指定“登录 URL”。 例如 `https://ssl-vpn.contoso.com`。

   - 对于“注销 URL”，输入将要发布的服务的主机头设为前缀的 BIG-IP APM 单一注销 (SLO) 终结点。 例如，`https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   提供 SLO URL 可确保用户注销后，在 BIG-IP 和 Azure AD 这两端同时终止用户会话。BIG-IP APM 还提供一个[选项](https://support.f5.com/csp/article/K12056)，用于在调用特定的应用程序 URL 时终止所有会话。

![该图像显示了基本 SAML 配置](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>从 TMOS v16 开始，SAML SLO 终结点已更改为 /saml/sp/profile/redirect/slo

4. 在退出 SAML 配置菜单之前选择“保存”，并跳过 SSO 测试提示。

观察“用户属性和声明”部分的属性，因为 Azure AD 会将这些属性和声明签发给用户进行 BIG-IP APM 身份验证。

![该图像显示了用户属性声明](media/f5-sso-vpn/user-attributes-claims.png)

可随意添加 BIG-IP 已发布服务可能预期收到的任何其他特定声明，同时请注意，在默认集之外定义的所有声明仅当它们存在于 Azure AD 中时才会作为填充属性签发。 同样，目录[角色或组](../hybrid/how-to-connect-fed-group-claims.md)成员身份也需要先针对 Azure AD 中的用户对象进行定义，然后才能作为声明签发。

![该图像显示了联合元数据下载链接](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD 创建的 SAML 签名证书的生命周期为三年，因此需要使用 Azure AD 发布的指南进行管理。

### <a name="azure-ad-authorization"></a>Azure AD 授权

默认情况下，Azure AD 仅向已被授予服务访问权限的用户签发令牌。

1. 仍然在应用程序的配置视图中，选择“用户和组”

2. 选择“+ 添加用户”，然后在“添加分配”菜单中选择“用户和组”

3. 在“用户和组”对话框中，添加有权访问 VPN 的用户组，然后选择“选择” > “分配”

![该图像显示了“添加用户”链接 ](media/f5-sso-vpn/add-user-link.png)

4. 此操作完成 SAML 联合身份验证信任的 Azure AD 部分。 现在可以设置 BIG-IP APM 以发布 SSL-VPN 服务，并配置相应的属性集用于完成联合身份验证信任，从而进行 SAML 预身份验证。

## <a name="big-ip-apm-configuration"></a>BIG-IP APM 配置

### <a name="saml-federation"></a>SAML 联合身份验证

以下部分创建 BIG-IP SAML 服务提供程序以及完成将 VPN 服务与 Azure AD 联合所需的相应 SAML IDP 对象。

1. 转到“访问” > “联合” > “SAML 服务提供程序” > “本地 SP 服务”，然后选择“创建”

![该图像显示了 BIG-IP SAML 配置](media/f5-sso-vpn/bigip-saml-configuration.png)

2. 输入之前在 Azure AD 中定义的“名称”和相同的“实体 ID”值，以及将用于连接到应用程序的主机 FQDN

![该图像显示了新 SAML SP 服务的创建](media/f5-sso-vpn/create-new-saml-sp.png)

   仅当实体 ID 与已发布 URL 的主机名部分不完全匹配，或者不是常规的基于主机名的 URL 格式时，才需要“SP 名称设置”。 如果实体 ID 为 `urn:ssl-vpn:contosoonline`，请提供要发布的应用程序的外部方案和主机名。

3. 向下滚动以选择新的 SAML SP 对象，然后选择“绑定/取消绑定 IDP 连接器”。

![该图像显示了与本地 SP 服务的联合身份验证的创建](media/f5-sso-vpn/federation-local-sp-service.png)

4. 选择“新建 IDP 连接器”，然后从下拉菜单中选择“从元数据”

![该图像显示了新 IDP 连接器的创建](media/f5-sso-vpn/create-new-idp-connector.png)

5. 浏览到之前下载的联合元数据 XML 文件，并为将表示外部 SAML IDP 的 APM 对象提供“标识提供者名称”

6. 选择“添加新行”，以选择新的 Azure AD 外部 IDP 连接器。

![该图像显示了外部 IDP 连接器](media/f5-sso-vpn/external-idp-connector.png)

7. 选择“更新”以将 SAML SP 对象绑定到 SAML IDP 对象，然后选择“确定”。

![该图像显示了使用 SP 的 SAML IDP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Webtop 配置

借助以下步骤，可以通过 BIG-IP 的专有 Web 门户向用户提供 SSL-VPN。

1. 转到“访问” > “Webtop” > “Webtop 列表”，然后选择“创建”。

2. 为门户命名，并将类型设置为“完整”。 例如 `Contoso_webtop`。

3. 调整其余首选项，然后选择“完成”。

![该图像显示了 Webtop 配置](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN 配置

VPN 功能由多个元素组成，每个元素控制整体服务的不同方面。

1. 转到“访问” > “连接/VPN” > “网络访问(VPN)” > “IPV4 租赁池”，然后选择“创建”。

2. 为要分配给 VPN 客户端的 IP 地址池提供名称。 例如，Contoso_vpn_pool

3. 将类型设置为“IP 地址范围”并提供起始 IP 和结束 IP，然后依次选择“添加”和“完成”。

![该图像显示了 VPN 配置](media/f5-sso-vpn/vpn-configuration.png)

网络访问列表使用 VPN 池中的 IP 和 DNS 设置、用户路由权限来预配服务，还可以在必要时启动应用程序。

1. 转到“访问” > “连接/VPN: 网络访问(VPN)” > ”网络访问列表”，然后选择”创建”。

2. 为 VPN 访问列表和标题提供名称（例如 Contoso-VPN），然后选择“完成”。

![该图像显示了网络访问列表中的 VPN 配置](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. 在顶部功能区中，选择“网络设置”，然后添加以下设置：

   • **支持的 IP 版本**：IPV4

   • **IPV4 租赁池**：选择之前创建的 VPN 池，例如 Contoso_vpn_pool

![该图像显示了 Contoso VPN 池](media/f5-sso-vpn/contoso-vpn-pool.png)

   “客户端设置”选项可用于在建立 VPN 时对客户端流量的路由方式强制实施限制。

4. 选择“完成”，然后转到“DNS/主机”选项卡添加设置：

   • **IPV4 主名称服务器**：环境 DNS 服务器的 IP

   • **DNS 默认域后缀**：此特定 VPN 连接的域后缀。 例如，contoso.com

![该图像显示了默认域后缀](media/f5-sso-vpn/domain-suffix.png)

[F5 文章](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html)提供有关如何根据偏好调整其余设置的详细信息。

现在，需要 BIG-IP 连接配置文件来配置 VPN 服务需要支持的每种 VPN 客户端类型的设置。 例如，Windows、OSX 和 Android。

1. 转到“访问” > “连接/VPN” > “连接” > “配置文件”，然后选择“添加”。

2. 提供配置文件名称，并将父配置文件设置为“/Common/connectivity”，例如 Contoso_VPN_Profile。

![该图像显示了新连接配置文件的创建](media/f5-sso-vpn/create-connectivity-profile.png)

F5 [文档](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html)提供有关客户端支持的更多详细信息。

## <a name="access-profile-configuration"></a>访问配置文件配置

配置 VPN 对象后，需要访问策略才能启用 SAML 身份验证服务。

1. 转到“访问” > “配置文件/策略” > “访问配置文件(按会话策略)”，然后选择“创建”

2. 提供配置文件名称，并为配置文件类型选择“全部”，例如 Contoso_network_access

3. 向下滚动以将至少一种语言添加到“接受的语言”列表中，然后选择“完成”

![该图像显示了常规属性](media/f5-sso-vpn/general-properties.png)

4. 在新访问配置文件的“按会话策略”字段中选择“编辑”，让可视化策略编辑器在单独的浏览器选项卡中启动。

![该图像显示了按会话策略](media/f5-sso-vpn/per-session-policy.png)

5. 选择 + 号，然后在弹出窗口中选择“身份验证” > “SAML 身份验证” > “添加项”。

6. 在 SAML 身份验证 SP 配置中，选择之前创建的 VPN SAML SP 对象，然后选择“保存”。

![该图像显示了 SAML 身份验证](media/f5-sso-vpn/saml-authentication.png)

7. 为 SAML 身份验证的“成功”分支选择 +。

8. 在“分配”选项卡中，选择“高级资源分配”，然后选择“添加项”

![该图像显示了高级资源分配](media/f5-sso-vpn/advance-resource-assign.png)

9. 在弹出窗口中，选择“新条目”，然后选择“添加/删除”。

10. 在子窗口中，选择“网络访问”，然后选择之前创建的网络访问配置文件

![该图像显示了新网络访问条目的添加](media/f5-sso-vpn/add-new-entry.png)

11. 切换到“Webtop”选项卡并添加之前创建的 Webtop 对象。

![该图像显示了 Webtop 对象的添加](media/f5-sso-vpn/add-webtop-object.png)

12. 选择“更新”，然后选择“保存”。

13. 选择上方“拒绝”框中的链接，将“成功”分支更改为“允许”，然后选择“保存”。

![该图像显示了新的可视化策略编辑器](media/f5-sso-vpn/vizual-policy-editor.png)

14. 通过选择“应用访问策略”并关闭可视化策略编辑器选项卡来提交这些设置。

![该图像显示了新访问策略管理器](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>发布 VPN 服务

完成所有设置后，APM 现在需要前端虚拟服务器来侦听连接到 VPN 的客户端。

1. 选择“本地流量” > “虚拟服务器” > “虚拟服务器列表”，然后选择“创建”。

2. 为 VPN 虚拟服务器提供“名称”，例如“VPN_Listener”。

3. 为虚拟服务器提供未使用的 IP“目标地址”，该地址已拥有用于接收客户端流量的路由

4. 将“服务端口”设置为“443 HTTPS”，并确保状态显示“已启用”

![该图像显示了新的虚拟服务器](media/f5-sso-vpn/new-virtual-server.png)

5. 将“HTTP 配置文件”设置为“http”并添加作为先决条件的一部分预配的公共 SSL 证书的”SSL 配置文件(客户端)”。

![该图像显示了 SSL 配置文件](media/f5-sso-vpn/ssl-profile.png)

6. 在“访问策略”下，将“访问配置文件”和“连接配置文件”设置为使用创建的 VPN 对象。

![该图像显示了访问策略](media/f5-sso-vpn/access-policy.png)

7. 完成时选择“完成”。

8.  SSL-VPN 服务现已发布，并可以通过 SHA 进行访问（直接通过其 URL 或通过 Microsoft 的应用程序门户）。

## <a name="additional-resources"></a>其他资源

- [密码身份验证的时代已结束，请改用无密码方式](https://www.microsoft.com/security/business/identity/passwordless)

- [什么是条件访问？](../conditional-access/overview.md)

- [用于实现远程办公的 Microsoft 零信任框架](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [与 Azure AD 进行完整应用程序集成的五个步骤](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>后续步骤

在远程 Windows 客户端上打开浏览器，然后浏览到 BIG-IP VPN 服务的 URL。 向 Azure AD 进行身份验证后，将看到 BIG-IP Webtop 门户和 VPN 启动器。

![该图像显示了 VPN 启动器](media/f5-sso-vpn/vpn-launcher.png)

选择 VPN 磁贴将安装 BIG-IP Edge 客户端并建立为 SHA 配置的 VPN 连接。
F5 VPN 应用程序也应该在 Azure AD 条件访问中作为目标资源可见。 请参阅我们的[指南](../conditional-access/concept-conditional-access-policies.md)，以生成条件访问策略并为用户实现 Azure AD [无密码身份验证](https://www.microsoft.com/security/business/identity/passwordless)。
