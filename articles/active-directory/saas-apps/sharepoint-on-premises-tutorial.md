---
title: 教程：Azure Active Directory 与本地 SharePoint 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与本地 SharePoint 之间实现联合身份验证。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: d168c2c442fba70d021e90daeecce5844adfb274
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463420"
---
# <a name="tutorial-implement-federated-authentication-between-azure-active-directory-and-sharepoint-on-premises"></a>教程：在 Azure Active Directory 与本地 SharePoint 之间实现联合身份验证

## <a name="scenario-description"></a>方案描述

在本教程中，你将在 Azure Active Directory 与本地 SharePoint 之间配置联合身份验证。 目的是使用户能够在 Azure Active Directory 上登录，并使用其标识访问 SharePoint 本地站点。

## <a name="prerequisites"></a>先决条件

若要执行配置，需要具备以下资源：
* 一个 Azure Active Directory 租户。 如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。
* 一个 SharePoint 2013 场或更高版本的场。

本文使用以下值：
- 企业应用程序名称（在 Azure AD 中）：`SharePoint corporate farm`
- 信任标识符（在 Azure AD 中）/领域（在 SharePoint 中）：`urn:sharepoint:federation`
- loginUrl（指向 Azure AD）：`https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed`
- SharePoint 站点 URL：`https://spsites.contoso.local/`
- SharePoint 站点回复 URL：`https://spsites.contoso.local/_trust/`
- SharePoint 信任配置名称：`AzureADTrust`
- Azure AD 测试用户的 UserPrincipalName：`AzureUser1@demo1984.onmicrosoft.com`

## <a name="configure-an-enterprise-application-in-azure-active-directory"></a>在 Azure Active Directory 中配置企业应用程序

若要在 Azure AD 中配置联合身份验证，需要创建专用的企业应用程序。 使用可在应用程序库中找到的预配置模板 `SharePoint on-premises` 来简化配置。

### <a name="create-the-enterprise-application"></a>创建企业应用程序

1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。
1. 在搜索框中，输入“SharePoint on-premises”。 从结果窗格中选择“SharePoint on-premises”。
1. 指定应用程序的名称（在本教程中为 `SharePoint corporate farm`），然后单击“创建”以添加该应用程序。
1. 在新企业应用程序中选择“属性”，然后检查“需要进行用户分配?”的值。 对于本方案，请将其值设置为“否”并单击“保存” 。

### <a name="configure-the-enterprise-application"></a>配置企业应用程序

在本部分，你将配置 SAML 身份验证，并定义在成功完成身份验证后要发送到 SharePoint 的声明。

1. 在企业应用程序 `SharePoint corporate farm` 的“概述”中选择“2. 设置单一登录”，然后在下一个对话框中选择“SAML” 。
 
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”窗格中的“编辑”图标  。

1. 在“基本 SAML 配置”部分执行以下步骤：

    1. 在“标识符”框中，确保存在此值：`urn:sharepoint:federation`。

    1. 在“回复 URL”框中，使用以下模式输入 URL：`https://spsites.contoso.local/_trust/`。

    1. 在“登录 URL”框中，使用以下模式输入 URL：`https://spsites.contoso.local/`。
    
    1. 选择“保存”。

1. 在“用户特性和声明”部分删除以下声明类型，这些声明类型没有作用，因为 SharePoint 不会使用它们来授予权限：
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

1. 设置现在应如下所示：

    ![基本 SAML 设置](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-saml-ids.png)

1. 复制稍后需要在 SharePoint 中使用的信息：

    - 在“SAML 签名证书”部分，下载“证书(Base64)”  。 此文件是 Azure AD 用来对 SAML 令牌进行签名的签名证书的公钥。 SharePoint 需使用此文件来验证传入的 SAML 令牌的完整性。

    - 在“设置 SharePoint corporate farm”部分，将“登录 URL”复制到记事本中，并将尾部字符串 /saml2 替换为 /wsfed   。
     
    > [!IMPORTANT]
    > 请务必将 /saml2 替换为 /wsfed，以确保 Azure AD 颁发 SharePoint 所需的 SAML 1.1 令牌 。

    - 在“设置 SharePoint corporate farm”部分，复制“注销 URL” 

## <a name="configure-sharepoint-to-trust-azure-active-directory"></a>配置 SharePoint 以信任Azure Active Directory

### <a name="create-the-trust-in-sharepoint"></a>在 SharePoint 中创建信任

在此步骤中，你将创建一个 SPTrustedLoginProvider 用于存储所需的配置，使 SharePoint 能够信任 Azure AD。 为此，需要提供前面从 Azure AD 复制的信息。 启动 SharePoint Management Shell 并运行以下脚本来创建信任：

```powershell
# Path to the public key of the Azure AD SAML signing certificate (self-signed), downloaded from the Enterprise application in the Azure AD portal
$signingCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("C:\AAD app\SharePoint corporate farm.cer")
# Unique realm (corresponds to the "Identifier (Entity ID)" in the Azure AD Enterprise application)
$realm = "urn:sharepoint:federation"
# Login URL copied from the Azure AD enterprise application. Make sure to replace "saml2" with "wsfed" at the end of the URL:
$loginUrl = "https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed"

# Define the claim types used for the authorization
$userIdentifier = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
$role = New-SPClaimTypeMapping "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming

# Let SharePoint trust the Azure AD signing certificate
New-SPTrustedRootAuthority -Name "Azure AD signing certificate" -Certificate $signingCert

# Create a new SPTrustedIdentityTokenIssuer in SharePoint
$trust = New-SPTrustedIdentityTokenIssuer -Name "AzureADTrust" -Description "Azure AD" -Realm $realm -ImportTrustCertificate $signingCert -ClaimsMappings $userIdentifier, $role -SignInUrl $loginUrl -IdentifierClaim $userIdentifier.InputClaimType
```

### <a name="configure-the-sharepoint-web-application"></a>配置 SharePoint Web 应用程序

在此步骤中，你将在 SharePoint 中配置一个 Web 应用程序，以信任前面创建的 Azure AD 企业应用程序。 请记住一些重要规则：
- 必须为 SharePoint Web 应用程序的默认区域启用 Windows 身份验证。 搜索爬网程序需要这种设置。
- 必须为使用 Azure AD 身份验证的 SharePoint URL 设置 HTTPS。

1. 创建或扩展 Web 应用程序。 本文介绍两种可能的配置：

    - 如果你要创建一个在默认区域中既使用 Windows 身份验证，又使用 Azure AD 身份验证的新 Web 应用程序，请执行以下操作：

        1. 启动 SharePoint Management Shell 并运行以下脚本：
            ```powershell
            # This script creates a new web application and sets Windows and Azure AD authentication on the Default zone
            # URL of the SharePoint site federated with Azure AD
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $applicationPoolManagedAccount = "Contoso\spapppool"

            $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$true
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $trustedAp = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust    
            
            New-SPWebApplication -Name "SharePoint - Azure AD" -Port 443 -SecureSocketsLayer -URL $trustedSharePointSiteUrl -ApplicationPool "SharePoint - Azure AD" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp, $trustedAp
            ```
        1. 打开 **SharePoint 管理中心** 站点。
        1. 在“系统设置”下，选择“配置备用访问映射”。 此时将打开“备用访问映射集合”框。
        1. 用新 Web 应用筛选显示内容，并确认自己是否看到类似下面的内容：
    
           ![Web 应用程序的备用访问映射](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-new-web-app.png)

    - 如果你要扩展现有 Web 应用程序以在新区域中使用 Azure AD 身份验证，请执行以下操作：

        1. 启动 SharePoint Management Shell 并运行以下脚本：

            ```powershell
            # This script extends an existing web application to set Azure AD authentication on a new zone
            # URL of the default zone of the web application
            $webAppDefaultZoneUrl = "http://spsites/"
            # URL of the SharePoint site federated with ADFS
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $ap = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust
            $wa = Get-SPWebApplication $webAppDefaultZoneUrl
            
            New-SPWebApplicationExtension -Name "SharePoint - Azure AD" -Identity $wa -SecureSocketsLayer -Zone Internet -Url $trustedSharePointSiteUrl -AuthenticationProvider $ap
            ```
        
        1. 打开 **SharePoint 管理中心** 站点。
        1. 在“系统设置”下，选择“配置备用访问映射”。 此时将打开“备用访问映射集合”框。
        1. 筛选已扩展的 Web 应用程序的显示内容，确认看到了如下所示的内容：
    
            ![已扩展的 Web 应用程序的备用访问映射](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-extended-zone.png)

创建 Web 应用程序后，可以创建根网站集，并将你的 Windows 帐户添加为主网站集管理员。

1. 为 SharePoint 站点创建证书

    由于 SharePoint URL 使用 HTTPS 协议 (`https://spsites.contoso.local/`)，因此必须在相应的 Internet Information Services (IIS) 站点上设置证书。 遵循以下步骤生成自签名证书：
    
    > [!IMPORTANT]
    > 自签名证书仅适用于测试目的。 在生产环境中，我们强烈建议改用由证书颁发机构颁发的证书。
    
    1. 打开 Windows PowerShell 控制台。
    1. 运行以下脚本以生成自签名证书，并将其添加到计算机的 MY 存储中：
    
        ```powershell
        New-SelfSignedCertificate -DnsName "spsites.contoso.local" -CertStoreLocation "cert:\LocalMachine\My"
        ```
    
1. 在 IIS 站点中设置证书
    1. 打开 Internet Information Services Manager 控制台。
    1. 在树视图中展开服务器，展开“站点”，选择站点“SharePoint - Azure AD”，然后选择“绑定”  。
    1. 依次选择“HTTPS 绑定”、“编辑” 。
    1. 在“TLS/SSL 证书”字段中，选择要使用的证书（例如前面创建的“spsites.contoso.local”），然后选择“确定” 。
    
    > [!NOTE]
    > 如果有多个 Web 前端服务器，则需要对每个服务器重复此操作。

现已完成 SharePoint 与 Azure AD 之间的基本信任配置。 让我们了解如何以 Azure Active Directory 用户身份登录到 SharePoint 站点。

## <a name="sign-in-as-a-member-user"></a>以成员用户的身份登录

Azure Active Directory 有[两种类型的用户](../external-identities/user-properties.md)：来宾用户和成员用户。 让我们从成员用户开始，它只是归属于你的组织的某个用户。

### <a name="create-a-member-user-in-azure-active-directory"></a>在 Azure Active Directory 中创建成员用户

1. 在 Azure 门户最左侧的窗格中，选择“Azure Active Directory”。 在“管理”窗格中选择“用户” 。

1. 选择屏幕顶部的“所有用户” > “新建用户”。

1. 选择“创建用户”，然后在用户属性中执行以下步骤。

    1. 在“名称”框中输入用户名。 我们使用“TestUser”。
  
    1. 在“用户名”框中输入 `AzureUser1@<yourcompanytenant>.onmicrosoft.com`。 此示例显示了 `AzureUser1@demo1984.onmicrosoft.com`：

       ![“用户”对话框](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-user.png)

    1. 选中“显示密码”复选框，并记下“密码”框中显示的值 。

    1. 选择“创建”。

    1. 现在可以与 `AzureUser1@demo1984.onmicrosoft.com` 共享网站，并允许此用户访问该网站。

### <a name="grant-permissions-to-the-azure-active-directory-user-in-sharepoint"></a>向 SharePoint 中的 Azure Active Directory 用户授予权限

用你的 Windows 帐户（网站集管理员）登录到 SharePoint 根网站集，然后单击“共享”。  
在对话框中，需要键入 userprincipalname 的确切值（例如 `AzureUser1@demo1984.onmicrosoft.com`），并仔细选择 name 声明结果（将鼠标移到结果上可查看其声明类型）

> [!IMPORTANT]
> 请仔细键入要邀请的用户的确切值，并在列表中选择相应的声明类型，否则无法共享。

![不包含 AzureCP 的人员选取器结果](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-no-azurecp.png)

存在此项限制的原因是 SharePoint 不会验证人员选取器中的输入，这可能会造成混淆，导致拼写错误或用户意外选择错误的声明类型。  
若要解决这种情况，可以使用名为 [AzureCP](https://yvand.github.io/AzureCP/) 的开源解决方案将 SharePoint 2019/2016/2013 与 Azure Active Directory 相连接，并根据 Azure Active Directory 租户解析输入。 有关详细信息，请参阅 [AzureCP](https://yvand.github.io/AzureCP/)。

下面是在配置了 AzureCP 的情况下运行的同一搜索操作：SharePoint 基于输入返回了实际用户：

![包含 AzureCP 的人员选取器结果](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-with-azurecp.png)

> [!IMPORTANT]
> AzureCP 不是 Microsoft 产品，并且不受 Microsoft 支持部门的支持。 若要在本地 SharePoint 场中下载、安装并配置 AzureCP，请参阅 [AzureCP](https://yvand.github.io/AzureCP/) 网站。 

Azure Active Directory 用户 `AzureUser1@demo1984.onmicrosoft.com` 现在可以使用其标识登录到 SharePoint 站点 `https://spsites.contoso.local/`。

## <a name="grant-permissions-to-a-security-group"></a>向安全组授予权限

### <a name="add-the-group-claim-type-to-the-enterprise-application"></a>将组声明类型添加到企业应用程序

1. 在企业应用程序 `SharePoint corporate farm` 的“概述”中，选择“2 .设置单一登录”。 

1. 在“用户特性和声明”部分，如果不存在组声明，请执行以下步骤：

    1. 依次选择“添加组声明”、“安全组”，并确保“源特性”设置为“组 ID”   
    1. 依次选中“自定义组声明的名称”、“将组作为角色声明发出”，然后单击“保存”  。
    1. “用户特性和声明”应如下所示：

    ![用户和组的声明](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-with-group.png)

### <a name="create-a-security-group-in-azure-active-directory"></a>在 Azure Active Directory 中创建安全组

让我们在 Azure Active Directory 中创建安全组：

1. 选择“Azure Active Directory” > “组”。

1. 选择“新建组”。

1. 填写“组类型”（安全性）、“组名称”（例如 `AzureGroup1`）和“成员身份类型”  。 将前面创建的用户添加为成员，然后单击“创建”：

    ![创建 Azure AD 安全组](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-group.png)
  
### <a name="grant-permissions-to-the-security-group-in-sharepoint"></a>向 SharePoint 中的安全组授予权限

Azure AD 安全组由其特性 `Id`（一个 GUID，例如 `E89EF0A3-46CC-45BF-93A4-E078FCEBFC45`）进行标识。  
在没有自定义声明提供程序的情况下，用户需要在人员选取器中键入组的确切值 (`Id`)，并选择相应的声明类型。 此操作既不方便，也不可靠。  
为了避免这种情况，本文使用了第三方声明提供程序 [AzureCP](https://yvand.github.io/AzureCP/) 在 SharePoint 中以友好方式查找组：

![人员选取器 - 搜索 Azure AD 组](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-azure-active-directory-group.png)

## <a name="manage-guest-users-access"></a>管理来宾用户访问权限

有两种类型的来宾帐户：

- B2B 来宾帐户：这些用户归属于外部 Azure Active Directory 租户
- MSA 来宾帐户：这些用户归属于 Microsoft 标识提供者（Hotmail、Outlook）或社交帐户提供商（Google 或类似公司）

默认情况下，Azure Active Directory 会将“唯一用户标识符”和声明“name”都设置为特性 `user.userprincipalname`。  
遗憾的是，此特性在来宾帐户中存在歧义，如下表所示：

| 在 Azure AD 中设置的源特性 | Azure AD 为 B2B 来宾使用的实际属性 | Azure AD 为 MSA 来宾使用的实际属性 | SharePoint 在验证身份时可依赖的属性 |
|--|--|--|--|
| `user.userprincipalname` | `mail`，例如：`guest@PARTNERTENANT` | `userprincipalname`，例如：`guest_outlook.com#EXT#@TENANT.onmicrosoft.com` | 不明确 |
| `user.localuserprincipalname` | `userprincipalname`，例如：`guest_PARTNERTENANT#EXT#@TENANT.onmicrosoft.com` | `userprincipalname`，例如：`guest_outlook.com#EXT#@TENANT.onmicrosoft.com` | `userprincipalname` |

总而言之，为了确保使用同一个特性标识所有来宾帐户，应将企业应用程序的标识符声明更新为使用特性 `user.localuserprincipalname` 而不是 `user.userprincipalname`。

### <a name="update-the-application-to-use-a-consistent-attribute-for-all-guest-users"></a>将应用程序更新为对所有来宾用户使用一致的特性

1. 在企业应用程序 `SharePoint corporate farm` 的“概述”中，选择“2 .设置单一登录”。
 
1. 在“设置 SAML 单一登录”页上，选择“用户特性和声明”窗格中的“编辑”图标  。

1. 在“用户特性和声明”部分执行以下步骤：

    1. 选择“唯一用户标识符(名称 ID)”，将其“源特性”属性更改为 user.localuserprincipalname，然后单击“保存”   。
    
    1. 选择 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`，将其“源特性”属性更改为 user.localuserprincipalname，然后单击“保存”  。
    
    1. “用户特性和声明”应如下所示：
    
    ![来宾的“用户属性和声明”](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-guests.png)

### <a name="invite-guest-users-in-sharepoint"></a>在 SharePoint 中邀请来宾用户

> [!NOTE]
> 本部分假设使用声明提供程序 AzureCP

在上一部分，你已将企业应用程序更新为对所有来宾帐户使用一致的特性。  
现在，需要更新 AzureCP 的配置以反映这种更改，并对来宾帐户使用特性 `userprincipalname`：

1. 打开 **SharePoint 管理中心** 站点。
1. 在“安全性”下，选择“AzureCP 全局配置” 。
1. 在“用户标识符属性”部分：将“‘来宾’用户的用户标识符”设置为“UserPrincipalName”  。
1. 单击“确定”

![AzureCP 来宾帐户配置](./media/sharepoint-on-premises-tutorial/sp-azurecp-attribute-for-guests.png)

现在可以邀请 SharePoint 站点中的任何来宾用户。

## <a name="configure-the-federation-for-multiple-web-applications"></a>配置多个 Web 应用程序的联合身份验证

该配置适用于单个 Web 应用程序，但如果你打算对多个 Web 应用程序使用相同的受信任标识提供者，则需要其他配置。 例如，假设你有一个独立的 Web 应用程序 `https://otherwebapp.contoso.local/`，现在想要对其启用 Azure Active Directory 身份验证。 为此，请配置 SharePoint 以传递 SAML WReply 参数，并在企业应用程序中添加 URL。

### <a name="configure-sharepoint-to-pass-the-saml-wreply-parameter"></a>配置 SharePoint 以传递 SAML WReply 参数

1. 在 SharePoint 服务器上，打开 SharePoint 201x Management Shell 并运行以下命令。 对于受信任标识令牌颁发者，请使用以前用过的同一名称。

```powershell
$t = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
$t.UseWReplyParameter = $true
$t.Update()
```

### <a name="add-the-urls-in-the-enterprise-application"></a>在企业应用程序中添加 URL

1. 在 Azure 门户中，选择“Azure Active Directory” > “企业应用程序”。 选择之前创建的企业应用程序名称，然后选择“单一登录”。

1. 在“设置 SAML 单一登录”页中，编辑“基本 SAML 配置” 。

1. 在“回复 URL (断言使用者服务 URL)”部分，添加需要通过 Azure Active Directory 将用户登录的其他所有 Web 应用程序的 URL（例如 `https://otherwebapp.contoso.local/`），然后单击“保存” 。

![指定其他 Web 应用程序](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-reply-urls.png)