---
title: 教程：在 Azure IoT 中心内证明 CA 证书的所有权 | Microsoft Docs
description: 教程：证明你拥有 Azure IoT 中心的 CA 证书
author: v-gpettibone
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: fb91c2e32b8b9ee3f3781f3930fe6fdfc5501e01
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732776"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>教程：证明 CA 证书的所有权

将根证书颁发机构 (CA) 证书或从属 CA 证书上传到 IoT 中心后，可以将其设置为自动验证，或手动证明你拥有证书。

## <a name="verify-certificate-automatically"></a>自动验证证书 

1. 在 Azure 门户中，导航到 IoT 中心，然后选择“设置”>“证书”。

2. 选择“添加”以添加新 CA 证书。

3. 在“证书名称”字段中输入显示名称，并选择添加要添加的 PEM 证书。

4. 要自动验证证书，请选中“在上传时将证书状态设置已验证”旁边的框。

  :::image type="content" source="media/tutorial-x509-prove-possession/skip-pop.png" alt-text="显示“跳过所有权证明”复选框所在位置的屏幕截图":::

5. 选择“保存”  。  证书显示在证书列表中，状态为“已验证”。

## <a name="verify-certificate-manually-after-upload"></a>上传后手动验证证书

1. 如果未选择在上传过程中自动验证证书，证书会显示在证书列表中，状态为“未验证”。 

2. 选择证书以查看“证书详细信息”对话框。

3. 在对话框中选择“生成验证码”。

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="证书详细信息对话框":::

4. 将验证码复制到剪贴板。 你必须将验证码设置为证书使用者。 例如，如果验证码为 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3，请将其添加为证书使用者，如下面的步骤所示。

5. 你可以通过三种方式生成验证证书：

    * 如果你使用的是 Microsoft 提供的 PowerShell 脚本，请运行 `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` 以创建名为 `VerifyCert4.cer` 的证书。 有关详细信息，请参阅[使用 Microsoft 提供的脚本](tutorial-x509-scripts.md)。

    * 如果你使用的是 Microsoft 提供的 Bash 脚本，请运行 `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` 以创建名为 `verification-code.cert.pem` 的证书。 有关详细信息，请参阅[使用 Microsoft 提供的脚本](tutorial-x509-scripts.md)。

    * 如果你使用 OpenSSL 生成证书，则必须先生成私钥，然后是证书签名请求 (CSR)：

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      然后，使用根 CA 配置文件创建证书（如下所示）或从属 CA 配置文件和 CSR。

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    有关详细信息，请参阅[使用 OpenSSL 创建测试证书](tutorial-x509-openssl.md)。

6. 在“证书详细信息”视图中选择新证书。

7. 上载证书后，选择“验证”。 CA 证书状态应更改为“已验证”。
