---
title: 使用 Azure Active Directory 应用程序代理远程访问 Power BI
description: 介绍如何将本地 Power BI 与 Azure Active Directory 应用程序代理相集成的基础知识。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: has-adal-ref
ms.openlocfilehash: e96a9d50698c1b9e0f9e0c8306d97124d2159985
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419268"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理远程访问 Power BI 移动版

本文讨论如何使用 Azure AD 应用程序代理将 Power BI 移动应用连接到 Power BI 报表服务器 (PBIRS) 和 SQL Server Reporting Services (SSRS) 2016 及更高版本。 通过该集成，未接入企业网络的用户可以从 Power BI 移动应用访问其 Power BI 报表，同时受到 Azure AD 身份验证的保护。 此保护包括条件访问和多重身份验证等[安全权益](application-proxy-security.md#security-benefits)。

## <a name="prerequisites"></a>先决条件

本文假定你已部署了报表服务和[启用了应用程序代理](../app-proxy/application-proxy-add-on-premises-application.md)。

- 启用应用程序代理需要在 Windows server 上安装连接器并完成[先决条件](../app-proxy/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)，使连接器能够与 Azure AD 服务通信。
- 发布 Power BI 时，建议使用相同的内部域和外部域。 若要了解有关自定义域的详细信息，请参阅[使用应用程序代理中的自定义域](./application-proxy-configure-custom-domain.md)。
- 此集成可用于 Power BI 移动版 iOS 和 Android 应用程序。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>步骤 1：配置 Kerberos 约束委派 (KCD)

对于使用 Windows 身份验证的本地应用程序，可以通过 Kerberos 身份验证协议以及一项称为 Kerberos 约束委派 (KCD) 的功能来实现单一登录 SSO。 在经过配置后，KCD 可让应用程序代理连接器获取用户的 Windows 令牌，即使该用户尚未直接登录到 Windows。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))和[用于通过应用程序代理单一登录到应用的 Kerberos 约束委派](application-proxy-configure-single-sign-on-with-kcd.md)。

Reporting Services 端无其他需要配置的内容。 只需确保具有有效的服务主体名称 (SPN)，就能进行正确的 Kerberos 身份验证。 另外，请确保为协商身份验证启用了 Reporting Services 服务器。

若要为 Reporting services 设置 KCD，请继续执行以下步骤。

### <a name="configure-the-service-principal-name-spn"></a>配置服务主体名称 (SPN)

SPN 是使用 Kerberos 身份验证的服务的唯一标识符。 需要确保为报表服务器提供正确的 HTTP SPN。 有关如何为报表服务器配置正确的服务主体名称 (SPN) 的信息，请参阅[为报表服务器注册服务主体名称 (SPN)](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server)。
可以结合 -L 选项运行 Setspn 命令，来验证是否已添加 SPN。 若要详细了解该命令，请参阅 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)。

### <a name="enable-negotiate-authentication"></a>启用协商身份验证

若要使报表服务器可以使用 Kerberos 身份验证，需要将报表服务器的身份验证类型配置为 RSWindowsNegotiate。 使用 rsreportserver.config 文件配置此设置。

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

有关详细信息，请参阅[修改 Reporting Services 配置文件](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config)和[在报表服务器上配置 Windows 身份验证](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server)。

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>请确保连接器是受信任的，以便可委派到添加到 Reporting Services 应用程序池帐户的 SPN
配置 KCD，使 Azure AD 应用程序代理服务能够将用户标识委派到 Reporting Services 应用程序池帐户。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序或本例中的 Reporting Services。

若要配置 KCD，请针对每个连接器计算机重复执行以下步骤：

1. 以域管理员身份登录域控制器，然后打开“Active Directory 用户和计算机”。
2. 找到运行连接器的计算机。
3. 双击此计算机，然后选择“委派”选项卡。
4. 将委派设置设为“信任此计算机仅委派指定服务”。 然后，选择“使用任意身份验证协议”。
5. 依次选择“添加”和“用户或计算机”。
6. 输入用于 Reporting Services 的服务帐户。 这是在 Reporting Services 配置中将 SPN 添加到其中的帐户。
7. 单击“确定”。 若要保存更改，请再次单击“确定”。

有关详细信息，请参阅[用于通过应用程序代理单一登录到应用的 Kerberos 约束委派](application-proxy-configure-single-sign-on-with-kcd.md)。

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>步骤 2：通过 Azure AD 应用程序代理发布 Report Services

现在可以配置 Azure AD 应用程序代理了。

1. 使用以下设置通过应用程序代理发布 Report Services。 有关如何通过应用程序代理发布应用程序的分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   - 内部 URL：输入连接器可以在企业网络中访问的报表服务器的 URL。 请确保此 URL 是可以从连接器安装所在的服务器进行访问的。 最佳做法是使用一级域名（如 `https://servername/`），以免通过应用程序代理发布的子路径出现问题。 例如，使用 `https://servername/`，而不是 `https://servername/reports/` 或 `https://servername/reportserver/`。
     > [!NOTE]
     > 建议使用与报表服务器的安全 HTTPS 连接。 若要详细了解操作说明，请参阅[在本机模式报表服务器上配置 SSL 连接](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server)。
   - 外部 URL：输入将与 Power BI 移动应用连接的公共 URL。 例如，如果使用的是自定义域，外部 URL 可能为 `https://reports.contoso.com`。 若要使用自定义域，请上传域证书，然后将 DNS 记录指向应用程序的默认 msappproxy.net 域。 有关详细步骤，请参阅[在 Azure AD 应用程序代理中使用自定义域](application-proxy-configure-custom-domain.md)。

   - 预身份验证方法：Azure Active Directory

2. 发布应用后，请执行以下步骤来配置单一登录设置：

   a. 在门户中的应用程序页上，选择“单一登录”。

   b. 对于“单一登录模式”，选择“集成 Windows 身份验证”。

   c. 将“内部应用程序 SPN”设置为此前设置的值。

   d. 针对你要代表你的用户使用的连接器选择“委派的登录标识”。 有关详细信息，请参阅[使用不同的本地标识和云标识](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)。

   e. 单击“保存”以保存更改。

要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户。

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>步骤 3：修改应用程序的回复 URI

必须先配置在步骤 2 中自动创建的应用程序注册，然后 Power BI 移动应用才能连接和访问 Report Services。

1. 在 Azure Active Directory 的“概览”页中，选择“应用注册”。
2. 在“所有应用程序”选项卡下，搜索在步骤 2 中创建的应用程序。
3. 依次选择应用程序和“身份验证”。
4. 根据所使用的平台，添加以下重定向 URI。

   为 Power BI 移动版 iOS 配置应用时，添加类型为公共客户端（移动和桌面）的以下重定向 URI：
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   为 Power BI 移动版 Android 配置应用时，添加类型为公共客户端（移动和桌面）的以下重定向 URI：
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 必须添加重定向 URI，这样应用程序才能正常运行。 如果同时为 Power BI 移动版 iOS 和 Android 配置应用，将类型为公共客户端（移动和桌面）的以下重定向 URI 添加到为 iOS 配置的重定向 URI 列表中：`urn:ietf:wg:oauth:2.0:oob`。

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>步骤 4：从 Power BI 移动应用进行连接

1. 在 Power BI 移动应用中，连接到 Reporting Services 实例。 为此，请输入通过应用程序代理发布的应用程序的“外部 URL”。

   ![具有外部 URL 的 Power BI 移动应用](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. 选择“连接”  。 此时会定向到 Azure Active Directory 登录页。

3. 输入用户的有效凭据，然后选择“登录”。 此时会看到 Reporting Services 服务器中的元素。

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>步骤 5：为托管设备配置 Intune 策略（可选）

可以使用 Microsoft Intune 来管理你公司的员工使用的客户端应用。 Intune 允许你使用诸如数据加密等功能和其他访问要求。 若要了解有关通过 Intune 进行应用管理的详细信息，请参阅“Intune 应用管理”。 若要使 Power BI 移动应用能够使用 Intune 策略，请执行以下步骤。

1. 依次转到“Azure Active Directory”和“应用注册”。
2. 注册本机客户端应用程序时，选择在步骤 3 中配置的应用程序。
3. 在应用程序页上，选择“API 权限”。
4. 单击“添加权限”。
5. 在“我的组织使用的 API”下，搜索“Microsoft 移动应用管理”并将其选中。
6. 向应用程序添加“DeviceManagementManagedApps.ReadWrite”权限
7. 单击“授予管理员许可”以授予对应用程序的访问权限。
8. 请参阅[如何创建和分配应用保护策略](/intune/app-protection-policies)来配置所需的 Intune 策略。

## <a name="troubleshooting"></a>疑难解答

如果应用程序在尝试加载报表超过几分钟后返回错误页面，则可能需要更改超时设置。 默认情况下，应用程序代理支持花费长达 85 秒来响应请求的应用程序。 若要将此设置延长为 180 秒，请在应用程序的应用代理设置页中将后端超时选为“长”。 有关如何创建快速可靠的报表的提示，请参阅 [Power BI 报表最佳实践](/power-bi/power-bi-reports-performance)。

使用 Azure AD 应用程序代理来启用 Power BI 移动应用以连接到本地 Power BI 报表服务器不受条件访问策略支持，因为该策略需要将 Microsoft Power BI 应用作为批准的客户端应用。

## <a name="next-steps"></a>后续步骤

- [使本机客户端应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)
- [在 Power BI 移动应用中查看本地报表服务器报表和 KPI](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
