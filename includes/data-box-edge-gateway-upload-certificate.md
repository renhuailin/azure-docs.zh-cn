---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "67448638"
---
适当的 SSL 证书可确保将加密信息发送到正确的服务器。 除加密以外，证书还允许进行身份验证。 你可以通过设备的 PowerShell 接口上传自己的受信任的 SSL 证书。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Set-HcsCertificate` cmdlet 上传证书。 出现提示时，请提供以下参数：

   - `CertificateFilePath` - 包含 .pfx 格式的证书文件的共享路径。
   - `CertificatePassword` - 用于保护证书的密码。
   - `Credentials` - 可访问包含证书的共享的用户名。 出现提示时向网络共享提供密码。

     以下示例显示了此 cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

