---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581607"
---
适当的 SSL 证书可确保将加密信息发送到正确的服务器。 除加密以外，证书还允许进行身份验证。 你可以通过设备的 PowerShell 接口上传你自己的受信任的 SSL 证书。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Set-HcsCertificate` cmdlet 上载证书。 出现提示时，请提供以下参数：

   - `CertificateFilePath` -以 *.pfx* 格式包含证书文件的共享的路径。
   - `CertificatePassword` -用于保护证书的密码。
   - `Credentials` -用于访问包含证书的共享的用户名。 出现提示时向网络共享提供密码。

     以下示例显示了此 cmdlet 的用法：

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
