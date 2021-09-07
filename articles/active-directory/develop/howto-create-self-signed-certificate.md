---
title: 创建自签名公用证书来对应用程序进行身份验证 | Azure
titleSuffix: Microsoft identity platform
description: 创建自签名公用证书来对应用程序进行身份验证。
services: active-directory
author: FaithOmbongi
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: ombongifaith
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: scenarios:getting-started
ms.openlocfilehash: 3cc084fe1b9df8a4ab4db5b926bb7b44646f17c2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030319"
---
# <a name="create-a-self-signed-public-certificate-to-authenticate-your-application"></a>创建自签名公用证书来对应用程序进行身份验证

对于服务主体，Azure Active Directory (Azure AD) 支持两种类型的身份验证：基于密码的身份验证（应用机密）和基于证书的身份验证。  尽管你可以轻松地在 Azure 门户中创建应用机密，但建议你让你的应用程序使用证书。

对于测试，你可以使用自签名公用证书，而不是使用证书颁发机构 (CA) 签名证书。 本文介绍了如何使用 Windows PowerShell 来创建和导出自签名证书。

> [!CAUTION]
> 建议仅将自签名证书用于开发，不要将其用于生产。

你可以为证书配置各种参数。 例如，加密和哈希算法、证书有效期和域名。 然后，根据应用程序的需要，导出带或不带私钥的证书。 

启动身份验证会话的应用程序需要私钥，而确认身份验证的应用程序需要公钥。 因此，如果要从 PowerShell 桌面应用向 Azure AD 进行身份验证，只需导出公钥（`.cer` 文件）并将其上传到 Azure 门户。 PowerShell 应用使用本地证书存储中的私钥来启动身份验证，并获取 Microsoft Graph 的访问令牌。

你的应用程序也可以从另一台计算机（例如 Azure 自动化）运行。 在这种情况下，请从本地证书存储中导出公钥和私钥对，并将公钥上传到 Azure 门户，将私钥（一个 `.pfx` 文件）上传到 Azure 自动化。 在 Azure 自动化中运行的应用程序将使用私钥来启动身份验证并获取 Microsoft Graph 的访问令牌。

本文使用 `New-SelfSignedCertificate` PowerShell cmdlet 创建自签名证书，并使用 `Export-Certificate` cmdlet 将其导出到可轻松访问的位置。 这些 cmdlet 内置于 Windows 的新式版本（Windows 8.1 和更高版本，以及 Windows Server 2012R2 和更高版本）中。 自签名证书将具有以下配置：

+ 2048 位的密钥长度。 虽然支持更长的值，但强烈建议你使用 2048 位大小来实现最佳的安全和性能组合。
+ 使用 RSA 加密算法。 Azure AD 当前仅支持 RSA。
+ 证书使用 SHA256 哈希算法进行签名。 Azure AD 还支持使用 SHA384 和 SHA512 哈希算法进行签名的证书。
+ 证书有效期只有一年。
+ 支持将此证书用于客户端和服务器身份验证。

> [!NOTE]
> 若要自定义证书的开始日期和到期日期以及其他属性，请参阅 [`New-SelfSignedCertificate` 参考](/powershell/module/pki/new-selfsignedcertificate?view=windowsserver2019-ps&preserve-view=true)。


## <a name="option-1--create-and-export-your-public-certificate-without-a-private-key"></a>选项 1：创建并导出不带私钥的公用证书

使用通过此方法创建的证书从计算机上运行的应用程序进行身份验证。 例如，从 Windows PowerShell 进行身份验证。

在提升权限的 PowerShell 提示符窗口中，运行以下命令并使 PowerShell 控制台会话保持打开状态。 将 `{certificateName}` 替换为你要为证书指定的名称。

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

上一命令中的 $cert 变量将证书存储在当前会话中，并允许你将其导出。 下面的命令采用 `.cer` 格式导出证书。 你还可以将其导出为 Azure 门户中支持的其他格式，包括 `.pem` 和 `.crt`。

```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

你的证书现在可以上传到 Azure 门户了。 上传后，检索用于对应用程序进行身份验证的证书指纹。


## <a name="option-2-create-and-export-your-public-certificate-with-its-private-key"></a>选项 2：创建并导出带私钥的公用证书

如果你的应用程序将从其他计算机或云（例如 Azure 自动化）运行，请使用此选项来创建证书及其私钥。

在提升权限的 PowerShell 提示符窗口中，运行以下命令并使 PowerShell 控制台会话保持打开状态。 将 `{certificateName}` 替换为你要为证书指定的名称。

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

上一命令中的 $cert 变量将证书存储在当前会话中，并允许你将其导出。 下面的命令采用 `.cer` 格式导出证书。 你还可以将其导出为 Azure 门户中支持的其他格式，包括 `.pem` 和 `.crt`。


```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

仍在同一会话中，为证书私钥创建密码，并将其保存在变量中。 在下面的命令中，将 `{myPassword}` 替换为要用来保护证书私钥的密码。

```powershell

$mypwd = ConvertTo-SecureString -String "{myPassword}" -Force -AsPlainText  ## Replace {myPassword}

```

现在，使用 `$mypwd` 变量中存储的密码保护并导出私钥。

```powershell

Export-PfxCertificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{privateKeyName}.pfx" -Password $mypwd   ## Specify your preferred location and replace {privateKeyName}

```

你的证书（`.cer` 文件）现在可以上传到 Azure 门户了。 你还有一个加密的私钥（`.pfx` 文件），他方无法读取该私钥。 上传后，检索用于对应用程序进行身份验证的证书指纹。


## <a name="optional-task-delete-the-certificate-from-the-keystore"></a>可选任务：从密钥存储中删除证书。

如果使用选项 2 创建了证书，则可以从个人存储中删除此密钥对。 首先，运行以下命令来检索证书指纹。

```powershell

Get-ChildItem -Path "Cert:\CurrentUser\My" | Where-Object {$_.Subject -Match "{certificateName}"} | Select-Object Thumbprint, FriendlyName    ## Replace {privateKeyName} with the name you gave your certificate

```

然后，复制显示的指纹，使用它来删除证书及其私钥。

```powershell

Remove-Item -Path Cert:\CurrentUser\My\{pasteTheCertificateThumbprintHere} -DeleteKey

```

### <a name="know-your-certificate-expiry-date"></a>了解证书到期日期

按照上述步骤创建的自签名证书具有有限的生存期，之后将会过期。 在 Azure 门户的“应用注册”部分，”证书和机密”屏幕会显示证书的到期日期。 如果你使用 Azure 自动化，则自动化帐户的“证书”屏幕会显示证书的到期日期。 按照前面的步骤创建一个新的自签名证书。

## <a name="next-steps"></a>后续步骤

[在 Azure Active Directory 中管理用于联合单一登录的证书](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
