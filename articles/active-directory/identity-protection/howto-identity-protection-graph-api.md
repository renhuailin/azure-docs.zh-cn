---
title: Microsoft Graph PowerShell SDK 和 Azure Active Directory 标识保护
description: 了解如何从 Azure Active Directory 中查询 Microsoft Graph 风险检测和相关信息
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21274636a38cb37ac62f2b73112d9346482fe573
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739963"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory 标识保护和 Microsoft Graph PowerShell SDK

Microsoft Graph 是 Microsoft 的统一 API 终结点，并且是 [Azure Active Directory 标识保护](./overview-identity-protection.md) API 的主页。 本文介绍了如何使用 [Microsoft Graph PowerShell SDK](/graph/powershell/get-started) 通过 PowerShell 获取风险用户详细信息。 如果组织要直接查询 Microsoft Graph API，则可以参阅[教程：使用 Microsoft Graph API 识别和修正风险](/graph/tutorial-riskdetection-api)一文来开始这段旅程。


## <a name="connect-to-microsoft-graph"></a>连接到 Microsoft Graph

通过 Microsoft Graph 访问“标识保护”数据需要执行四个步骤：

- [创建证书](#create-a-certificate)
- [创建新的应用注册](#create-a-new-app-registration)
- [配置 API 权限](#configure-api-permissions)
- [配置有效凭据](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>创建证书

在生产环境中，你将使用生产证书颁发机构颁发的证书，但在此示例中，我们将使用自签名证书。 运行下面的 PowerShell 命令，以创建并导出证书。

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>创建新的应用注册

1. 在 Azure 门户中，浏览到“Azure Active Directory” > “应用注册”。
1. 选择“新注册”。
1. 在“创建”页中，执行以下步骤：
   1. 在“名称”文本框中，键入应用程序的名称例如：Azure AD 风险检测 API）。
   1. 在“受支持的帐户类型”下，选择将使用 API 的帐户类型。
   1. 选择“注册”。
1. 记下“应用程序(客户端) ID”和“目录(租户) ID”，因为你稍后将需要用到这些项。

### <a name="configure-api-permissions"></a>配置 API 权限

在此示例中，我们配置应用程序权限，允许在无人参与的情况下使用此示例。 若要向将登录的用户授予权限，请改为选择“委托的权限”。 若要详细了解不同权限类型，请参阅 [Microsoft 标识平台中的权限和同意](../develop/v2-permissions-and-consent.md#permission-types)一文。

1. 从创建的“应用程序”中，选择“API 权限”。
1. 在“配置权限”页中，在顶部工具栏中单击“添加权限”。
1. 在“添加 API 访问权限”页中，单击“选择 API”。
1. 在“选择 API”页中，选择“Microsoft Graph”，然后单击“选择”。
1. 在“请求 API 权限”页中： 
   1. 选择“应用程序权限”。
   1. 选中“`IdentityRiskEvent.Read.All`”和“`IdentityRiskyUser.Read.All`”旁边的复选框。
   1. 选择“添加权限”。
1. 选择“为域授予管理员同意” 

### <a name="configure-a-valid-credential"></a>配置有效凭据

1. 从创建的“应用程序”中，选择“证书和机密”。
1. 在“证书”下，选择“上传证书”。
   1. 在随即打开的窗口中，选择之前导出的证书。
   1. 选择“添加”。
1. 记下证书的“指纹”，因为在下一步中你将需要用到此信息。

## <a name="list-risky-users-using-powershell"></a>使用 PowerShell 列出风险用户

若要启用查询 Microsoft Graph 这项功能，需要使用 `Install-Module Microsoft.Graph` 命令在 PowerShell 窗口中安装 `Microsoft.Graph` 模块。

修改下面的变量以包含前面步骤中生成的信息，然后使用 PowerShell 整体运行这些变量来获取风险用户详细信息。

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>后续步骤

- [Microsoft Graph PowerShell SDK 入门](/graph/powershell/get-started)
- [教程：使用 Microsoft Graph API 识别和修正风险](/graph/tutorial-riskdetection-api)
- [Microsoft Graph 概述](https://developer.microsoft.com/graph/docs)
- [在不是用户的情况下获取访问权限](/graph/auth-v2-service)
- [Azure AD 标识保护服务根](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory 标识保护](./overview-identity-protection.md)
