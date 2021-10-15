---
title: 实现对 SharePoint 的远程访问 - Azure Active Directory 应用程序代理
description: 介绍如何将本地 SharePoint 服务器与 Azure Active Directory 应用程序代理相集成的基础知识。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: d5e46dec7fb1ecde4a7b00ef0f963c621a295c61
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232349"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理实现对 SharePoint 的远程访问

此分步指南介绍如何将本地 SharePoint 场与 Azure Active Directory (Azure AD) 应用程序代理进行集成。

## <a name="prerequisites"></a>先决条件

若要执行配置，需要具备以下资源：
- 一个 SharePoint 2013 场或更高版本的场。
- Azure AD 租户及包含应用程序代理的 Azure AD 计划。 详细了解 [Azure AD 计划和定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。
- Azure AD 租户中[已验证的自定义域](../fundamentals/add-custom-domain.md)。
- 已与 Azure AD Connect 同步的本地 Active Directory（用户可通过该 Azure AD Connect [登录 Azure](../hybrid/plan-connect-user-signin.md)）。
- 在企业域中的计算机上安装并运行的应用程序代理连接器。

使用应用程序代理配置 SharePoint 时，需要使用两个 URL：
- 外部 URL，对最终用户可见并在 Azure AD 中确定。 此 URL 可使用自定义域。 了解有关[使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)的详细信息。
- 内部 URL，仅在企业域中是已知的，并且不能直接使用。

> [!IMPORTANT]
> 若要确保这些链接正确映射，请按以下建议配置内部 URL：
> - “使用 HTTPS”。
> - 不使用自定义端口。
> - 在企业域名系统 (DNS) 中，创建指向 SharePoint WFE（或负载均衡器）的主机 (A)，而非别名 (CName)。

本文使用以下值：
- 内部 URL：`https://sharepoint`
- 外部 URL：`https://spsites-demo1984.msappproxy.net/`
- SharePoint Web 应用的应用程序池帐户：`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>步骤 1：在使用应用程序代理的 Azure AD 中配置应用程序

在此步骤中，用户可在使用应用程序代理的 Azure Active Directory 租户中创建应用程序。 用户可设置外部 URL，并指定内部 URL，以便稍后用于 SharePoint。

1. 根据说明，按以下设置创建应用程序。 有关分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * 内部 URL：SharePoint 内部 URL，稍后将在 SharePoint 中设置，如 `https://sharepoint`。
   * 预身份验证：Azure Active Directory
   * 转换标头中的 URL：否
   * 转换应用程序主体中的 URL：否

   ![发布 SharePoint 应用程序](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 发布应用程序后，按以下步骤配置单一登录设置：

   1. 在门户中的应用程序页上，选择“单一登录”。
   1. 对于“单一登录模式”，选择“集成 Windows 身份验证”。
   1. 将“内部应用程序 SPN”设置为此前设置的值。 在此示例中，该值为 `HTTP/sharepoint`。
   1. 在“委派的登录标识”下，为 Active Directory 林配置选择最适合的选项。 例如，如果林中只有一个 Active Directory 域，则选择“本地 SAM 帐户名称”（如以下屏幕截图所示）。 但如果用户与 SharePoint 和应用程序代理连接器服务器不在同一个域中，则选择“本地用户主体名称”（未显示在屏幕截图中）。

   ![为 SSO 配置集成 Windows 身份验证](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>步骤 2：配置 SharePoint Web 应用

SharePoint Web 应用必须使用 Kerberos 和相应的备用访问映射进行配置，才能与 Azure AD 应用程序代理一起正常工作。 下面是两种可能的选项：

- 创建新 Web 应用且仅使用默认区域。 这是首选项，因为此选项可提供最佳 SharePoint 体验（例如，SharePoint 所生成的电子邮件警报中的链接始终指向默认区域）。
- 扩展现有 Web 应用以在非默认区域中配置 Kerberos。

> [!IMPORTANT]
> 无论使用哪个区域，SharePoint Web 应用的应用程序池帐户必须是域帐户，Kerberos 才能正常工作。

### <a name="provision-the-sharepoint-web-application"></a>预配 SharePoint Web 应用

- 创建新 Web 应用且仅使用默认区域（首选项）：

    1. 启动 SharePoint Management Shell 并运行以下脚本：

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. 打开 **SharePoint 管理中心** 站点。
    1. 在“系统设置”下，选择“配置备用访问映射”。 此时将打开“备用访问映射集合”框。
    1. 用新 Web 应用筛选显示内容，并确认自己是否看到类似下面的内容：

       ![Web 应用的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 将现有 Web 应用扩展到新区域（如果无法使用默认区域）：

    1. 启动 SharePoint Management Shell 并运行以下脚本：

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. 打开 **SharePoint 管理中心** 站点。
    1. 在“系统设置”下，选择“配置备用访问映射”。 此时将打开“备用访问映射集合”框。
    1. 用已扩展的 Web 应用筛选显示内容，并确认自己是否看到类似下面的内容：

        ![已扩展应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>确保 SharePoint Web 应用在域帐户下运行

若要确定运行 SharePoint Web 应用的应用程序池的帐户，并确保该帐户是域帐户，请按照以下步骤操作：

1. 打开 **SharePoint 管理中心** 站点。
1. 转到“安全”并选择“配置服务帐户”。
1. 选择“Web 应用程序池 - YourWebApplicationName”。

   ![用于配置服务帐户的选项](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 确认“为此组件选择帐户”是否返回域帐户，并记住该帐户，因为下一步需要使用此内容。

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>确保为 Extranet 区域的 IIS 站点配置 HTTPS 证书

由于内部 URL 使用 HTTPS 协议 (`https://SharePoint/`)，因此必须在 Internet Information Services (IIS) 站点上设置证书。

1. 打开 Windows PowerShell 控制台。
1. 运行以下脚本，以生成自签名证书并将其添加到计算机的 MY 存储中：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自签名证书仅适用于测试目的。 在生产环境中，强烈建议改用由证书颁发机构颁发的证书。

1. 打开 Internet Information Services Manager 控制台。
1. 在树视图中展开服务器，展开“站点”，选择“SharePoint - AAD 代理”站点，并选择“绑定”。
1. 选择“HTTPS 绑定”，然后选择“编辑”。
1. 在“TLS/SSL 证书”字段中，选择“SharePoint”证书，然后选择“确定”。

现在可以通过 Azure AD 应用程序代理从外部访问 SharePoint 站点。

## <a name="step-3-configure-kerberos-constrained-delegation"></a>步骤 3：配置 Kerberos 约束委派

用户最初在 Azure AD 中进行身份验证，然后通过 Azure AD 代理连接器使用 Kerberos 连接到 SharePoint。 若要让连接器代表 Azure AD 用户获取 Kerberos 令牌，必须使用协议转换配置 Kerberos 约束委派 (KCD)。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>为 SharePoint 服务帐户设置 SPN

在本文中，内部 URL 是 `https://sharepoint`，因此服务主体名称 (SPN) 是 `HTTP/sharepoint`。 用户必须将这些值替换为与其环境相对应的值。
若要为 SharePoint 应用程序池帐户 `Contoso\spapppool` 注册 SPN `HTTP/sharepoint`，请以域管理员身份在命令提示符下运行以下命令：

`setspn -S HTTP/sharepoint Contoso\spapppool`

`Setspn` 命令会先搜索 SPN，然后再进行添加。 如果此 SPN 已经存在，则用户会看到“SPN 值重复”错误。 在这种情况下，如果未在正确的应用程序池帐户下设置现有 SPN，则考虑将其删除。 用户可结合 -L 选项运行 `Setspn` 命令，来验证是否成功添加 SPN。 若要详细了解该命令，请参阅 [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>确保连接器受信任，可委派给添加到 SharePoint 应用程序池帐户的 SPN

配置 KCD，使 Azure AD 应用程序代理服务能够向 SharePoint 应用程序池帐户委托用户标识。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序（在本例中为 SharePoint）。

若要配置 KCD，请对每个连接器计算机执行以下步骤：

1. 以域管理员身份登录域控制器，然后打开“Active Directory 用户和计算机”。
1. 查找运行 Azure AD 代理连接器的计算机。 在本例中，是运行 SharePoint Server 的计算机。
1. 双击此计算机，然后选择“委派”选项卡。
1. 确保将委派选项设置为“仅信任此计算机来委派指定的服务”。 然后，选择“使用任意身份验证协议”。
1. 选择“添加”按钮，选择“用户或计算机”，然后找到 SharePoint 应用程序池帐户。 例如：`Contoso\spapppool`。
1. 在 SPN 列表中，选择此前为服务帐户创建的 SPN。
1. 选择“确定”，然后再次选择“确定”以保存更改内容。
  
   ![委派设置](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

现在，可以使用外部 URL 登录 SharePoint，并使用 Azure 进行身份验证。

## <a name="troubleshoot-sign-in-errors"></a>排查登录错误

如果无法登录站点，则可在连接器日志中获取有关该问题的详细信息：在运行连接器的计算机中，打开事件查看器，转到“应用程序和服务日志” > “Microsoft” > “AadApplicationProxy” > “连接器”，然后检查“管理员”日志。

## <a name="next-steps"></a>后续步骤

* [使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
