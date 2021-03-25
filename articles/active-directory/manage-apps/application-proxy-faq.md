---
title: Azure Active Directory 应用程序代理常见问题解答
description: 了解有关使用 Azure AD 应用程序代理向远程用户发布内部本地应用程序的常见问题的解答 (FAQ)。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: edd2ec633bd78ce1a596782deab57105e9d7f1c3
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487740"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) 应用程序代理常见问题解答

本页解答有关 Azure Active Directory (Azure AD) 应用程序代理的常见问题。

## <a name="enabling-azure-ad-application-proxy"></a>启用 Azure AD 应用程序代理

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>需有哪个许可证才能使用 Azure AD 应用程序代理？

若要使用 Azure AD 应用程序代理，必须有 Azure AD Premium P1 或 P2 许可证。 有关许可的详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>如果许可证过期，租户中的 Azure AD 应用程序代理会发生什么情况？
如果许可证过期，应用程序代理将自动禁用。 应用程序信息将保存长达一年。

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>“启用应用程序代理”按钮为何灰显？

请确保至少有 Azure AD Premium P1 或 P2 许可证，并已安装 Azure AD 应用程序代理连接器。 成功安装第一个连接器后，Azure AD 应用程序代理服务将自动启用。

## <a name="connector-configuration"></a>连接器配置

### <a name="why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version"></a>为何我的连接器仍使用旧版本，而不自动升级到最新版本？

原因可能是更新服务未正常工作，或者没有新的更新可供该服务安装。

如果更新服务正在运行，且事件日志（“应用程序和服务日志”->“Microsoft”->“AadApplicationProxy”->“更新服务”->“管理员”）中未记录任何错误，则表示该服务处于正常状态。 

> [!IMPORTANT]
> 只会发布主版本供自动升级。 建议定期手动更新连接器。 有关新发行版、发布类型（下载、自动升级）、bug 修复和新功能的详细信息，请参阅 [Azure AD 应用程序代理：版本发布历史记录](application-proxy-release-version-history.md)。

若要手动升级连接器：

-  下载最新版本的连接器。 （在 Azure 门户上的“应用程序代理”下可以找到）。 还可以在 [Azure AD 应用程序代理：版本发布历史记录](application-proxy-release-version-history.md)中找到链接。
-   安装程序将重启 Azure AD 应用程序代理连接器服务。 在某些情况下，如果安装程序无法替换所有文件，则可能需要重新启动服务器。 因此，我们建议在开始升级之前关闭所有应用程序（例如事件查看器）。
-   运行安装程序。 升级过程很快就能完成，且不需要提供任何凭据，连接器不会重新注册。

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>应用程序代理连接器服务是否可以在非默认的用户上下文中运行？

不可以，不支持这种方案。 默认设置为：

- Microsoft AAD 应用程序代理连接器 - WAPCSvc - 网络服务
- Microsoft AAD 应用程序代理连接器更新服务 - WAPCUpdaterSvc - NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>具有“全局管理员”或“应用程序管理员”角色的来宾用户是否可为（来宾）租户注册连接器？

目前无法做到这一点。 注册尝试始终在用户的主租户上进行。

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>我的后端应用程序托管在多个 Web 服务器上，需要用户会话持久性（粘性）。 如何实现会话持久性？ 

有关建议，请参阅[应用程序代理连接器和应用程序的高可用性与负载均衡](application-proxy-high-availability-load-balancing.md)。

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>是否支持对从连接器服务器到 Azure 的流量执行 TLS 终止（TLS/HTTPS 检查或加速）？

应用程序代理连接器对 Azure 执行基于证书的身份验证。 TLS 终止（TLS/HTTPS 检查或加速）会破坏这种身份验证方法，因此不受支持。 从连接器到 Azure 的流量必须绕过任何正在执行 TLS 终止的设备。  

### <a name="is-tls-12-required-for-all-connections"></a>是否需要对所有连接使用 TLS 1.2？
是。 为了向我们的客户提供一流的加密，应用程序代理服务将访问限制为仅允许使用 TLS 1.2 协议。 这些更改已自 2019 年 8 月 31 日起逐步推出并生效。 请确保将所有客户端-服务器和浏览器-服务器组合更新为使用 TLS 1.2，以便保持连接到应用程序代理服务。 这包括用户用来访问那些通过应用程序代理发布的应用程序的客户端。 请查看如何为 [Office 365 中的 TLS 1.2](/microsoft-365/compliance/prepare-tls-1.2-in-office-365) 做准备，了解有用的参考和资源。

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>是否可以在连接器服务器与后端应用程序服务器之间放置转发代理设备？
可以，从连接器版本 1.5.1526.0 开始支持此方案。 请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>是否应创建一个专用帐户来向 Azure AD 应用程序代理注册连接器？

没理由这样做。 任何全局管理员或应用程序管理员帐户都能实现此目的。 注册过程完成后，不会使用安装期间输入的凭据， 而会向连接器颁发一个证书，此后，该证书将用于身份验证。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>如何监视 Azure AD 应用程序代理连接器的性能？

将连同连接器一起安装一些性能监视器计数器。 查看管理共享：  

1. 选择“开始”，键入“性能”并按 Enter。
2. 选择“性能监视器”，然后单击绿色的 **+** 图标。
3. 添加要监视的“Microsoft AAD 应用程序代理连接器”计数器。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 应用程序代理连接器是否必须位于资源所在的同一子网中？

连接器不需要位于同一子网中。 但是，它需要对资源进行名称解析（DNS、hosts 文件），并建立必要的网络连接（路由到资源、在资源上打开端口，等等）。 有关建议，请参阅[使用 Azure Active Directory 应用程序代理时的网络拓扑注意事项](application-proxy-network-topology.md)。

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>可以在哪些版本的 Windows Server 上安装连接器？

应用程序代理需要 Windows Server 2012 R2 或更高版本。 目前适用于 Windows Server 2019 的 HTTP2 存在限制。 若要成功地在 Windows Server 2019 上使用连接器，需要添加以下注册表项并重启服务器：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
```

## <a name="application-configuration"></a>应用程序配置

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>我收到了有关证书无效或密码可能不正确的错误

上传 SSL 证书后，门户上出现消息“证书无效，密码可能不正确”。

下面是用于排查此错误的一些提示：
- 检查证书是否有问题。 将证书安装在本地计算机上。 如果未遇到任何问题，则表明该证书是正常的。
- 确保密码不包含任何特殊字符。 用于测试时，密码只能包含字符 0-9、A-Z 和 a-z。
- 如果证书是使用 Microsoft 软件密钥存储提供程序创建的，则必须使用 RSA 算法。

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>默认后端超时和“长”后端超时的时长是多少？ 是否可以延长超时？

默认时长为 85 秒。 “长”超时设置为 180 秒。 无法延长超时限制。

### <a name="can-a-service-principal-manage-application-proxy-using-powershell-or-microsoft-graph-apis"></a>服务主体是否可以使用 Powershell 或 Microsoft Graph API 管理应用程序代理？

不能，目前不支持。

### <a name="what-happens-if-i-delete-cwap_authsecret-the-client-secret-in-the-app-registration"></a>如果在应用注册中删除 CWAP_AuthSecret（客户端机密），会发生什么情况？

创建 Azure AD 应用程序代理应用时，会自动将客户端机密（也称为 *CWAP_AuthSecret*）添加到应用程序对象（应用注册）。

客户端机密的有效期为一年。 在当前的有效客户端机密过期之前，会自动创建有效期为一年的新客户端机密。 始终有三个 CWAP_AuthSecret 客户端机密保留在应用程序对象中。 

> [!IMPORTANT]
> 删除 CWAP_AuthSecret 会破坏 Azure AD 应用程序代理的预身份验证。 请不要删除 CWAP_AuthSecret。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>如何更改应用程序加载的登陆页？

在“应用程序注册”页中，可将主页 URL 更改为登陆页的所需外部 URL。 从“我的应用”或 Office 365 门户启动应用程序时，将加载指定的页。 有关配置步骤，请参阅[使用 Azure AD 应用程序代理为发布的应用设置自定义主页](./application-proxy-configure-custom-home-page.md)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>是否只能发布基于 IIS 的应用程序？ 可以发布非 Windows Web 服务器上运行的 Web 应用程序吗？ 连接器是否必须安装在装有 IIS 的服务器上？

不是，对于发布的应用程序没有 IIS 方面的要求。 可以发布不是 Windows Server 的服务器上运行的 Web 应用程序。 但是，可能无法对非 Windows Server 使用预身份验证，具体取决于 Web 服务器是否支持“协商”（Kerberos 身份验证）。 在安装连接器的服务器上不需要 IIS。

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>是否可以配置应用程序代理来添加 HSTS 标头？
应用程序代理不会自动在 HTTPS 响应中添加 HTTP 严格传输安全性标头，但如果标头位于所发布的应用程序发送的原始响应中，则应用程序代理会保留标头。 我们正在开发一项可靠的设置来启用此功能，此项工作已纳入路线图。 如果你对用于在响应中添加此标头的预览版功能感兴趣，请联系 aadapfeedback@microsoft.com 获取详细信息。

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>设置 Kerberos 约束委派 (KCD) 时，何时应使用 PrincipalsAllowedToDelegateToAccount 方法？

当连接器服务器与 Web 应用程序服务帐户不在同一个域中时，将使用 PrincipalsAllowedToDelegateToAccount 方法。 在此情况下需要使用基于资源的约束委派。
如果连接器服务器与 Web 应用程序服务帐户在同一个域中，则可以使用“Active Directory 用户和计算机”在每个连接器计算机帐户中配置委派设置，使这些帐户可委派到目标 SPN。

如果连接器服务器与 Web 应用程序服务帐户在不同的域中，则使用基于资源的委派。 委派权限是在目标 Web 服务器和 Web 应用程序服务帐户中配置的。 此约束委派方法相对较新。 该方法是在 Windows Server 2012 中引入的，该版本通过允许资源（Web 服务）所有者控制哪些计算机和服务帐户可以向资源委派，来支持跨域委派。 没有任何 UI 可帮助完成此项配置，因此需要使用 PowerShell。
有关详细信息，请参阅白皮书[了解应用程序代理的 Kerberos 约束委派](https://aka.ms/kcdpaper)。

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>NTLM 身份验证是否适用于 Azure AD 应用程序代理？

NTLM 身份验证不能用作预身份验证或单一登录方法。 仅当可以直接在客户端与发布的 Web 应用程序之间协商时，才能使用 NTLM 身份验证。 使用 NTLM 身份验证通常会导致浏览器中出现登录提示。

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>是否可以在 B2B IWA 单一登录方案中使用登录标识“本地用户主体名称”或“本地 SAM 帐户名”？

不可以，这种用法无效，因为 Azure AD 中的来宾用户没有上述任何登录标识所需的属性。

在这种情况下，将回退到“用户主体名称”。 有关 B2B 方案的更多详细信息，请阅读[向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限](../external-identities/hybrid-cloud-to-on-premises.md)。

## <a name="pass-through-authentication"></a>直通身份验证

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>对于所发布的使用直通身份验证的应用程序，是否可以使用条件访问策略？

只会对 Azure AD 中已成功完成预身份验证的用户强制实施条件访问策略。 直通身份验证不会触发 Azure AD 身份验证，因此无法强制实施条件访问策略。 使用直通身份验证时，必须在本地服务器上来实施 MFA 策略（如果可能），或者通过在 Azure AD 应用程序代理中启用预身份验证来实施 MFA 策略。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>是否可以发布存在客户端证书身份验证要求的 Web 应用程序？

不可以，不支持此方案，因为应用程序代理将终止 TLS 流量。  

## <a name="remote-desktop-gateway-publishing"></a>远程桌面网关发布

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>如何通过 Azure AD 应用程序代理发布远程桌面网关？

请参阅[使用 Azure AD 应用程序代理发布远程桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>是否可以在远程桌面网关发布方案中使用 Kerberos 约束委派（单一登录 - Windows 集成身份验证）？

不可以，不支持这种方案。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>我的用户不使用 Internet Explorer 11，预身份验证方案不适用于他们。 这是正常情况吗？

是的，这是预期的情况。 预身份验证方案需要某个 ActiveX 控件，而第三方浏览器并不支持此控件。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>是否支持远程桌面 Web 客户端 (HTML5)？

支持，此方案目前为公共预览版。 请参阅[使用 Azure AD 应用程序代理发布远程桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>配置预身份验证方案后，我发现用户必须完成身份验证两次：第一次在 Azure AD 登录窗体中进行，第二次在 RDWeb 登录窗体中进行。 这是正常情况吗？ 如何减为登录一次？

是的，这是预期的情况。 如果用户的计算机已加入 Azure AD，则用户会登录到自动 Azure AD。 用户只需在 RDWeb 登录窗体中提供凭据。

## <a name="sharepoint-publishing"></a>SharePoint 发布

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>如何通过 Azure AD 应用程序代理发布 SharePoint？

请参阅[使用 Azure AD 应用程序代理实现对 SharePoint 的远程访问](application-proxy-integrate-with-sharepoint-server.md)。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>是否可以使用 SharePoint 移动应用 (iOS/Android) 来访问发布的 SharePoint 服务器？

[SharePoint 移动应用](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)目前不支持 Azure Active Directory 预身份验证。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory 联合身份验证服务 (AD FS) 发布 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>是否可以将 Azure AD 应用程序代理用作 AD FS 代理（类似于 Web 应用程序代理）？

不是。 Azure AD 应用程序代理设计为与 Azure AD 配合工作，不满足充当 AD FS 代理的要求。

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense-and-remote-desktop-web-client-html5"></a>WebSocket 支持是否适用于除 QlikSense 和远程桌面 Web 客户端 (HTML5) 以外的应用程序？

目前，WebSocket 协议支持仍为公共预览版，可能不适用于其他应用程序。 有些客户可以在其他应用程序中成功使用 WebSocket 协议，但顺利程度各不相同。 如果你要测试此类方案，我们很乐意听取你的结果。 请将反馈发送到 aadapfeedback@microsoft.com。

目前无法通过 Azure AD 应用程序代理使用 Windows 管理中心 (WAC) 内的功能（事件日志、PowerShell 和远程桌面服务）。

## <a name="link-translation"></a>链接转换

### <a name="does-using-link-translation-affect-performance"></a>使用链接转换是否影响性能？

是。 链接转换会影响性能。 应用程序代理服务将扫描应用程序以获取硬编码的链接，将链接替换为发布的相应外部 URL，然后将其呈现给用户。 

为获得最佳性能，我们建议通过配置[自定义域](./application-proxy-configure-custom-domain.md)来使用相同的内部和外部 URL。 如果无法使用自定义域，可以使用“我的应用安全登录扩展”或手机版 Microsoft Edge 浏览器来改进链接转换性能。 请参阅[使用 Azure AD 应用程序代理重定向已发布应用的硬编码链接](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="wildcards"></a>通配符

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>如何使用通配符发布自定义域名相同、但协议不同（一个为 HTTP，另一个为 HTTPS）的两个应用程序？

无法直接支持此方案。 对于此方案，可行的做法如下：

1. 使用通配符将 HTTP 和 HTTPS URL 发布为单独的应用程序，但为每个应用程序指定不同的自定义域。 此配置将起作用，因为应用程序具有不同的外部 URL。

2. 通过通配符应用程序发布 HTTPS URL。 使用以下应用程序代理 PowerShell cmdlet 单独发布 HTTP 应用程序：
   - [应用程序代理应用程序管理](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [应用程序代理连接器管理](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
