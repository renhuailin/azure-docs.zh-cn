---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916908"
---
如果你还没有证书，则可以在本教程中使用自签名证书。 自签名证书是未由证书颁发机构签名 (CA) 的安全证书。 它们不提供证书颁发机构签署的证书的所有安全保证。 

# <a name="windows"></a>[Windows](#tab/windows)

在 Windows 上，使用 PowerShell 的 [new-selfsignedcertificate](/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet 生成证书。

1. 执行此 PowerShell 命令来生成自签名证书。 根据应用程序的需要修改 `-Subject` 参数，并修改 Azure AD B2C 租户名称。 还可调整 `-NotAfter` 日期，为证书指定不同的过期日期。

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

1. 打开“管理用户证书” > “当前用户” > “个人” > “证书” > “yourappname.yourtenant.onmicrosoft.com”   
1. 选择该证书 >“操作” > “所有任务” > “导出”  
1. 选择“是” > “下一步” > “是，导出私钥” > “下一步”   
1. 接受“导出文件格式”的默认值
1. 提供证书的密码。

为了使 Azure AD B2C 接受 .pfx 文件密码，必须在 Windows 证书存储导出实用程序（而不是 AES256-SHA256）中通过 TripleDES-SHA1 选项对密码进行加密。

# <a name="macos"></a>[macOS](#tab/macos)

在 macOS 上，使用密钥链 Access 中的 [证书助手](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) 生成证书。

1. 按照说明如何 [在 Mac 上的密钥链 Access 中创建自签名证书](https://support.apple.com/guide/keychain-access/kyca8916/mac)。
1. 在 Mac 上的密钥链 Access 应用程序中，选择你创建的证书。
1. 选择 "文件 > 导出项"。
1. 选择文件名以保存证书。 例如， **自签名证书. p12**。 
1. 对于 " **文件格式**"，请选择 " **个人信息交换 ( p12)**。
1. 选择“保存”。
1. 输入 **密码**，然后 **验证** 密码。
1. 将文件扩展名替换为 `.pfx` 。 例如， **self-signed-certificate**。 

---
