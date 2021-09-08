---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 19065a8a0bc828c30997edd05e9b268657e0fcb1
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221526"
---
如果你还没有证书，则可以使用自签名证书。 自签名证书是未由证书颁发机构 (CA) 签署的安全证书，不提供由 CA 签名的证书的安全保障。 

# <a name="windows"></a>[Windows](#tab/windows)

在 Windows 上，可在 PowerShell 中使用 [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) cmdlet 来生成证书。

1. 运行以下 PowerShell 命令来生成自签名证书。 根据应用程序的需要修改 `-Subject` 参数，并修改 Azure AD B2C 租户名称。 还可调整 `-NotAfter` 日期，为证书指定不同的过期日期。

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. 打开“管理用户证书” > “当前用户” > “个人” > “证书” > “yourappname.yourtenant.onmicrosoft.com”   。
1. 选择该证书，然后依次选择“操作” > “所有任务” > “导出”。
1. 选择“是” > “下一步” > “是，导出私钥” > “下一步”   。
1. 接受“导出文件格式”的默认值。
1. 提供证书的密码。

要让 Azure AD B2C 接受 .pfx 文件密码，必须在 Windows 证书存储导出实用工具中使用 TripleDES-SHA1 选项，而不是 AES256-SHA256 对此密码进行加密。

# <a name="macos"></a>[macOS](#tab/macos)

在 macOS 上，使用 Keychain Access 中的[证书助手](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0)生成证书。

1. 按照如何[在 Mac 上的 Keychain Access 中创建自签名证书](https://support.apple.com/guide/keychain-access/kyca8916/mac)的说明进行操作。
1. 在 Mac 上的 Keychain Access 应用中，选择你创建的证书。
1. 选择“文件” > “导出项” 。
1. 选择文件名以保存证书。 例如：self-signed-certificate.p12。
1. 对于“文件格式”，请选择“个人信息交换(.p12)” 。
1. 选择“保存”  。
1. 在“密码”和“验证”框中输入密码 。
1. 将文件扩展名替换为 .pfx。 例如：self-signed-certificate.pfx。

---
