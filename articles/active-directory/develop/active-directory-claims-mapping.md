---
title: 自定义 Azure AD 租户应用声明 (PowerShell)
titleSuffix: Microsoft identity platform
description: 了解如何为特定 Azure Active Directory 租户中的应用程序自定义在令牌中发出的声明。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 302ab2b6f3060213a79b88ce6358cd842f74a425
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425547"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>为租户中的特定应用自定义在令牌中发出的声明

“声明”是标识提供者在为某个用户颁发的令牌中陈述的有关该用户的信息。 租户管理员使用声明自定义为其租户中的特定应用程序自定义令牌中发出的声明。 可以使用声明映射策略执行以下操作：

- 选择在令牌中包含的声明。
- 创建尚不存在的声明类型。
- 选择或更改在特定声明中发出的数据的源。

声明自定义支持配置适用于 WS-Fed、SAML、OAuth 和 OpenID Connect 协议的声明映射策略。

> [!NOTE]
> 此功能替换并取代了通过 Azure 门户提供的[声明自定义](active-directory-saml-claims-customization.md)。 在同一应用程序中，如果使用门户以及本文档中详细介绍的 Microsoft Graph/PowerShell 方法自定义声明，则为该应用程序颁发的令牌会忽略门户中的配置。 通过本文档中详细介绍的方法进行的配置不会在门户中进行反映。

在本文中，我们会演练几个常见方案，它们可帮助你理解如何使用[声明映射策略类型](reference-claims-mapping-policy-type.md)。

## <a name="get-started"></a>入门

以下示例将创建、更新、链接和删除服务主体的策略。 声明映射策略只能分配给服务主体对象。 如果你是 Azure AD 新手，我们建议在继续学习这些示例之前，先[了解如何获取 Azure AD 租户](quickstart-create-new-tenant.md)。

创建声明映射策略时，还可以根据令牌中的目录架构扩展属性发出声明。 使用与扩展属性对应的 ExtensionID，而不是 `ClaimsSchema` 元素中的 ID。  有关扩展属性的更多信息，请参阅[使用目录架构扩展属性](active-directory-schema-extensions.md)。

> [!NOTE]
> 配置声明映射策略需要 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。 PowerShell 模块为预览版，而 Azure 中的声明映射和令牌创建运行时已正式发布。 预览版 PowerShell 模块的更新可能需要更新或更改配置脚本。 

若要开始，请执行以下步骤：

1. 首先请下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 运行 [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0-preview) 命令，登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 若要查看组织中创建的所有策略，请运行以下命令。 在以下方案中，建议在大多数操作之后运行此命令，以检查是否按预期创建策略。

   ``` powershell
   Get-AzureADPolicy
   ```

接下来，创建声明映射策略并将其分配给服务主体。  有关常见方案，请参阅以下示例：
- [省略令牌中的基本声明](#omit-the-basic-claims-from-tokens)
- [在令牌中包括 EmployeeID 和 TenantCountry 作为声明](#include-the-employeeid-and-tenantcountry-as-claims-in-tokens)
- [在令牌中使用声明转换](#use-a-claims-transformation-in-tokens)

创建声明映射策略后，将应用程序配置为承认令牌将包含自定义声明。  有关详细信息，请阅读[安全注意事项](#security-considerations)。

## <a name="omit-the-basic-claims-from-tokens"></a>省略令牌中的基本声明

在此示例中创建一个策略，它会从颁发给链接的服务主体的令牌中删除[基本声明集](reference-claims-mapping-policy-type.md#claim-sets)。

1. 创建声明映射策略。 此策略（链接到特定服务主体）会从令牌中删除基本声明集。
   1. 若要创建该策略，请运行以下命令：

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：

      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 Microsoft Graph API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到你的 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>在令牌中包括 EmployeeID 和 TenantCountry 作为声明

在此示例中创建一个策略，它会向颁发给链接的服务主体的令牌添加 EmployeeID 和 TenantCountry。 EmployeeID 在 SAML 令牌和 JWT 中都作为名称声明类型发出。 TenantCountry 在 SAML 令牌和 JWT 中都作为国家/地区声明类型发出。 在此示例中，我们继续在令牌中包含基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）向令牌添加 EmployeeID 和 TenantCountry 声明。
   1. 若要创建该策略，请运行以下命令：

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"employeeid"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

      > [!WARNING]
      > 为目录扩展属性定义声明映射策略时，请使用 `ExtensionID` 属性，而不是 `ClaimsSchema` 数组主体中的 `ID` 属性。

   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：

      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 Microsoft Graph API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到你的 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>在令牌中使用声明转换

在此示例中创建一个策略，它会向颁发给链接的服务主体的 JWT 发出自定义声明“JoinedData”。 此声明包含通过将用户对象的 extensionattribute1 属性中存储的数据与“.sandbox”联接所创建的值。 在此示例中，我们在令牌中排除基本声明集。

1. 创建声明映射策略。 此策略（链接到特定服务主体）向令牌添加 EmployeeID 和 TenantCountry 声明。
   1. 若要创建该策略，请运行以下命令：

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 若要查看新策略并获取其 ObjectId，请运行以下命令：

      ``` powershell
      Get-AzureADPolicy
      ```
1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。
   1. 若要查看组织的所有服务主体，可以[查询 Microsoft Graph API](/graph/traverse-the-graph)。 或者，在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 中登录到你的 Azure AD 帐户。
   2. 获取服务主体的 ObjectId 后，运行以下命令：

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>安全注意事项

接收令牌的应用程序依赖于这样一个事实：即声明值是由 Azure AD 权威颁发的，不能篡改。 但当你通过声明映射策略修改令牌内容时，上述事实可能不再适用。 应用程序必须明确承认令牌已被声明映射策略的创建者修改，才能防止被恶意参与者创建的声明映射策略破坏。 通过以下其中一种方式可实现此目的：

- [配置自定义签名密钥](#configure-a-custom-signing-key)
- 或[更新应用程序清单](#update-the-application-manifest)，以接受映射的声明。
 
否则，Azure AD 将返回 [`AADSTS50146` 错误代码](reference-aadsts-error-codes.md#aadsts-error-codes)。

### <a name="configure-a-custom-signing-key"></a>配置自定义签名密钥

对于多租户应用，应使用自定义签名密钥。  请勿在应用清单中设置 `acceptMappedClaims`。 如果在 Azure 门户中设置应用，你会在租户中获取一个应用注册对象和一个服务主体。  该应用使用 Azure 全局登录密钥，该密钥不能用于自定义令牌中的声明。  若要获取令牌中的自定义声明，请从证书创建自定义登录密钥，并将其添加到服务主体。  对于测试，可以使用自签名的证书。 配置自定义签名密钥后，应用程序代码需要[验证令牌签名密钥](#validate-token-signing-key)。

向服务主体添加以下信息：

- 私钥（作为[密钥凭据](/graph/api/resources/keycredential)）
- 密码（作为[密码凭据](/graph/api/resources/passwordcredential)）
- 公钥（作为[密钥凭据](/graph/api/resources/keycredential)）

从证书的 PFX 文件导出中提取 base-64 编码的私钥和公钥。 确保用于“签名”的 `keyCredential` 的 `keyId` 与 `passwordCredential` 的 `keyId` 相匹配。 可通过获取证书指纹的哈希来生成 `customkeyIdentifier`。

#### <a name="request"></a>请求

下面显示了用于向服务主体添加自定义签名密钥的 HTTP PATCH 请求的格式。  为便于阅读，`keyCredentials` 属性中的“密钥”值已缩短。 该值是 base-64 编码的。 对于私钥，属性用法为“签名”。 对于公钥，属性用法为“验证”。

```
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json
Authorization: Bearer {token}

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=contoso"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=contoso"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "mypassword"
        }
    ]
}
```

#### <a name="configure-a-custom-signing-key-using-powershell"></a>使用 PowerShell 配置自定义签名密钥

使用 PowerShell [实例化 MSAL 公共客户端应用程序](msal-net-initializing-client-applications.md#initializing-a-public-client-application-from-code)，并使用[授权代码授予](v2-oauth2-auth-code-flow.md)流获取 Microsoft Graph 的委托权限访问令牌。 使用访问令牌调用 Microsoft Graph，并为服务主体配置自定义签名密钥。 配置自定义签名密钥后，应用程序代码需要[验证令牌签名密钥](#validate-token-signing-key)。

若要运行脚本，你需要：
1. 应用程序服务主体的对象 ID，可在 Azure 门户中的[企业应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)中的应用程序条目的“概述”边栏选项卡中找到。
2. 应用注册，用于登录应用和获取用于调用 Microsoft Graph 的访问令牌。 在 Azure 门户的[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)中的应用程序条目的“概述”边栏选项卡中获取此应用的应用程序（客户端）ID。 应用注册应具有以下配置：
    - “移动和桌面应用程序”平台配置中列出的“http://localhost”的重定向 URI
    - 在“API 权限”中，Microsoft Graph 委托权限 Application.ReadWrite.All 和 User.Read（确保对这些权限授予管理员同意）  
3. 登录以获取 Microsoft Graph 访问令牌的用户。 用户应是以下 Azure AD 管理角色之一（更新服务主体所必需）：
    - 云应用管理员
    - 应用程序管理员
    - 全局管理员
4. 要配置为应用程序的自定义签名密钥的证书。 可以创建自签名证书，也可以从你信任的证书颁发机构获取一个证书。 脚本中使用了以下证书组件：
    - 公钥（通常为 .cer 文件）
    - PKCS#12 格式的私钥（.pfx 文件）
    - 私钥的密码（pfx 文件）

> [!IMPORTANT]
> 私钥必须采用 PKCS#12 格式，因为 Azure AD 不支持其他格式类型。 使用 Microsoft Graph 通过包含证书信息的 `keyCredentials` 修补服务主体时，使用错误格式可能会导致错误“证书无效：密钥值是无效证书”。

```powershell

$fqdn="fourthcoffeetest.onmicrosoft.com" # this is used for the 'issued to' and 'issued by' field of the certificate
$pwd="mypassword" # password for exporting the certificate private key
$location="C:\\temp" # path to folder where both the pfx and cer file will be written to

# Create a self-signed cert
$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx"
 
# Export the public and private keys
Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile

$ClientID = "<app-id>"
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "fourthcoffeetest.onmicrosoft.com"
$redirectURL = "http://localhost" # this reply URL is needed for PowerShell Core 
[string[]] $Scopes = "https://graph.microsoft.com/.default"
$pfxpath = $pfxFile # path to pfx file
$cerpath = $cerFile # path to cer file
$SPOID = "<service-principal-id>"
$graphuri = "https://graph.microsoft.com/v1.0/serviceprincipals/$SPOID"
$password = $pwd  # password for the pfx file
 
 
# choose the correct folder name for MSAL based on PowerShell version 5.1 (.Net) or PowerShell Core (.Net Core)
 
if ($PSVersionTable.PSVersion.Major -gt 5)
    { 
        $core = $true
        $foldername =  "netcoreapp2.1"
    }
else
    { 
        $core = $false
        $foldername = "net45"
    }
 
# Load the MSAL/microsoft.identity/client assembly -- needed once per PowerShell session
[System.Reflection.Assembly]::LoadFrom((Get-ChildItem C:/Users/<username>/.nuget/packages/microsoft.identity.client/4.32.1/lib/$foldername/Microsoft.Identity.Client.dll).fullname) | out-null
  
$global:app = $null
  
$ClientApplicationBuilder = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($ClientID)
[void]$ClientApplicationBuilder.WithAuthority($("$loginURL/$tenantdomain"))
[void]$ClientApplicationBuilder.WithRedirectUri($redirectURL)
 
$global:app = $ClientApplicationBuilder.Build()
  
Function Get-GraphAccessTokenFromMSAL {
    [Microsoft.Identity.Client.AuthenticationResult] $authResult  = $null
    $AquireTokenParameters = $global:app.AcquireTokenInteractive($Scopes)
    [IntPtr] $ParentWindow = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
    if ($ParentWindow)
    {
        [void]$AquireTokenParameters.WithParentActivityOrWindow($ParentWindow)
    }
    try {
        $authResult = $AquireTokenParameters.ExecuteAsync().GetAwaiter().GetResult()
    }
    catch {
        $ErrorMessage = $_.Exception.Message
        Write-Host $ErrorMessage
    }
     
    return $authResult
}
  
$myvar = Get-GraphAccessTokenFromMSAL
if ($myvar)
{
    $GraphAccessToken = $myvar.AccessToken
    Write-Host "Access Token: " $myvar.AccessToken
    #$GraphAccessToken = "eyJ0eXAiOiJKV1QiL ... iPxstltKQ"
    
 
    #  this is for PowerShell Core
    $Secure_String_Pwd = ConvertTo-SecureString $password -AsPlainText -Force
 
    # reading certificate files and creating Certificate Object
    if ($core)
    {
        $pfx_cert = get-content $pfxpath -AsByteStream -Raw
        $cer_cert = get-content $cerpath -AsByteStream -Raw
        $cert = Get-PfxCertificate -FilePath $pfxpath -Password $Secure_String_Pwd
    }
    else
    {
        $pfx_cert = get-content $pfxpath -Encoding Byte
        $cer_cert = get-content $cerpath -Encoding Byte
        # Write-Host "Enter password for the pfx file..."
        # calling Get-PfxCertificate in PowerShell 5.1 prompts for password
        # $cert = Get-PfxCertificate -FilePath $pfxpath
        $cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($pfxpath, $password)
    }
 
    # base 64 encode the private key and public key
    $base64pfx = [System.Convert]::ToBase64String($pfx_cert)
    $base64cer = [System.Convert]::ToBase64String($cer_cert)
 
    # getting id for the keyCredential object
    $guid1 = New-Guid
    $guid2 = New-Guid
 
    # get the custom key identifier from the certificate thumbprint:
    $hasher = [System.Security.Cryptography.HashAlgorithm]::Create('sha256')
    $hash = $hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($cert.Thumbprint))
    $customKeyIdentifier = [System.Convert]::ToBase64String($hash)
 
    # get end date and start date for our keycredentials
    $endDateTime = ($cert.NotAfter).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
    $startDateTime = ($cert.NotBefore).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
 
    # building our json payload
    $object = [ordered]@{    
    keyCredentials = @(       
         [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid1
            startDateTime = $startDateTime 
            type = "X509CertAndPassword"
            usage = "Sign"
            key = $base64pfx
            displayName = "CN=fourthcoffeetest" 
        },
        [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid2
            startDateTime = $startDateTime 
            type = "AsymmetricX509Cert"
            usage = "Verify"
            key = $base64cer
            displayName = "CN=fourthcoffeetest"   
        }
        )  
    passwordCredentials = @(
        [ordered]@{
            customKeyIdentifier = $customKeyIdentifier
            keyId = $guid1           
            endDateTime = $endDateTime
            startDateTime = $startDateTime
            secretText = $password
        }
    )
    }
 
    $json = $object | ConvertTo-Json -Depth 99
    Write-Host "JSON Payload:"
    Write-Output $json
 
    # Request Header
    $Header = @{}
    $Header.Add("Authorization","Bearer $($GraphAccessToken)")
    $Header.Add("Content-Type","application/json")
 
    try 
    {
        Invoke-RestMethod -Uri $graphuri -Method "PATCH" -Headers $Header -Body $json
    } 
    catch 
    {
        # Dig into the exception to get the Response details.
        # Note that value__ is not a typo.
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__ 
        Write-Host "StatusDescription:" $_.Exception.Response.StatusDescription
    }
 
    Write-Host "Complete Request"
}
else
{
    Write-Host "Fail to get Access Token"
}
```

#### <a name="validate-token-signing-key"></a>验证令牌签名密钥
启用了声明映射的应用必须通过将 `appid={client_id}` 追加到其 [OpenID Connect 元数据请求](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)来验证令牌签名密钥。 下面是你应该使用的 OpenID 连接元数据文档的格式：

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>更新应用程序清单

对于单租户应用，可以在[应用程序清单](reference-app-manifest.md)中将 `acceptMappedClaims` 属性设置为 `true`。  如 [apiApplication 资源类型](/graph/api/resources/apiapplication#properties)中所述，这样可以让应用程序使用声明映射，而无需指定自定义签名密钥。 

> [!WARNING]
> 对于多租户应用，请勿将 `acceptMappedClaims` 属性设置为 `true`，这可能会允许恶意参与者为应用创建声明映射策略。

这确实会要求请求令牌的受众使用经过验证的 Azure AD 租户域名，这意味着应确保设置 `Application ID URI`（在应用程序清单中由 `identifierUris` 表示），例如将其设置为 `https://contoso.com/my-api` 或（仅使用默认租户名）`https://contoso.onmicrosoft.com/my-api`。

如果不使用经过验证的域，Azure AD 将返回 `AADSTS501461` 错误代码以及消息“AcceptMappedClaims 仅支持与应用程序 GUID 匹配的令牌受众或经过验证的租户域中的受众。请更改资源标识符，或使用特定于应用程序的签名密钥。”

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[声明映射策略类型](reference-claims-mapping-policy-type.md)参考文章。
- 若要了解如何通过 Azure 门户自定义 SAML 令牌中颁发的声明，请参阅[如何：为企业应用程序自定义 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)
- 若要详细了解扩展属性，请参阅[在声明中使用目录架构扩展属性](active-directory-schema-extensions.md)。
