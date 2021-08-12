---
title: 教程：使用 Microsoft 脚本创建 Azure IoT 中心的 x.509 测试证书 | Microsoft Docs
description: 教程：使用自定义脚本为 Azure IoT 中心创建 CA 和设备证书
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 8170b6529ca38c54d3030514a79948678861f3bd
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214899"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>教程：使用 Microsoft 提供的脚本创建测试证书

Microsoft 提供了 PowerShell 和 Bash 脚本来帮助你了解如何创建你自己的 x.509 证书，并将在 IoT 中心进行身份验证。 这些脚本位于 GitHub [存储库](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)中。 它们仅供演示之用。 所创建的证书不得用于生产。 证书包含硬编码密码（“1234”）并在 30 天后过期。 对于生产环境，你需要使用自己的最佳做法来创建证书和生存期管理。

## <a name="powershell-scripts"></a>PowerShell 脚本

### <a name="step-1---setup"></a>步骤 1 — 安装程序

获取 OpenSSL for Windows。 请参阅 <https://www.openssl.org/docs/faq.html#MISC4> 了解下载位置或浏览 <https://www.openssl.org/source/> 从源生成。 然后运行初步脚本：

1. 将此 GitHub [存储库](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) 中的脚本复制到要使用的本地目录。 所有文件将被创建为此目录的子文件夹。

1. 以管理员的身份启动 PowerShell。

1. 更改为你在其中加载脚本的目录。

1. 在命令行中，将环境变量“`$ENV:OPENSSL_CONF`”设置到 openssl 配置文件 (openssl.cnf) 所在的目录。

1. 运行 `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` 以便 PowerShell 可以运行脚本。

1. 运行 `. .\ca-certs.ps1`。 这会将脚本的函数引入 PowerShell 全局命名空间。

1. 运行 `Test-CACertsPrerequisites`。 PowerShell 使用 Windows 证书存储管理证书。 此命令验证当前的证书未来不会出现名称冲突，以及 OpenSSL 已正确设置。

### <a name="step-2---create-certificates"></a>步骤 2 — 创建证书

运行 `New-CACertsCertChain [ecc|rsa]`。 建议将 ECC 用于 CA 证书，但这不是必需的。 此脚本使用以下 CA 和中间证书更新你的目录和 Windows 证书存储：

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

运行该脚本后，将新的 CA 证书 (RootCA.pem) 添加到 IoT 中心：

1. 转到 IoT 中心并导航到“证书”。

1. 选择 **添加** 。

1. 输入 CA 证书的显示名字。

1. 要跳过所有权证明，请选中“在上传时将证书状态设置已验证”旁边的框。

1. 上传 CA 证书。

1. 选择“保存”。

### <a name="optional-step-3---prove-possession"></a>（可选）步骤 3 - 证明所有权

如果未选择在上传时自动验证证书，请手动证明所有权：

1. 选择新的 CA 证书。

1. 选择“证书详细信息”对话框中的“生成验证码”。  有关详细信息，请参阅[证明对 CA 证书的所有权](tutorial-x509-prove-possession.md)。

1. 创建包含验证码的证书。 例如，如果验证代码为 `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`，请运行以下代码，在包含主题 `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` 的工作目录中创建新证书。 脚本可创建证书 `VerifyCert4.cer`。

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. 在“证书详细信息”对话框中将 `VerifyCert4.cer` 上传到 IoT 中心。

1. 选择“验证”  。

### <a name="step-4---create-a-new-device"></a>步骤 4 — 创建新设备

为 IoT 中心创建设备：

1. 在 IoT 中心中，导航到“IoT 设备”部分。

1. 添加 ID 为 `mydevice` 的新设备。

1. 对于身份验证，请选择“X.509 CA 签名”。

1. 运行 `New-CACertsDevice mydevice` 创建新的设备证书。 这会在你的工作目录中创建以下文件：

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>步骤 5 — 测试设备证书

转到[测试证书身份验证](tutorial-x509-test-certificate.md)，以确定设备证书是否可在 IoT 中心进行身份验证。 你将需要证书的 PFX 版本 `mydevice.pfx`。

### <a name="step-6---cleanup"></a>步骤 6 — 清理

从“开始”菜单中，打开“管理计算机证书”并导航到“证书-本地计算机”>“个人”。  删除“Azure IoT CA TestOnly*”颁发的任何证书。 同样地，从 >“受信任的根证书颁发机构”>“证书”>“中间证书颁发机构”>“证书”中删除相应的证书。

## <a name="bash-scripts"></a>Bash 脚本

### <a name="step-1---setup"></a>步骤 1 — 安装程序

1. 开启 Bash。

1. 更改为要使用的目录。 所有文件都将在此目录中创建。

1. 将 `*.cnf` 和复制 `*.sh` 到你的工作目录中。

### <a name="step-2---create-certificates"></a>步骤 2 — 创建证书

1. 运行 `./certGen.sh create_root_and_intermediate`。 此操作将在 certs 目录下创建以下文件：

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. 转到 IoT 中心并导航到“证书”。

1. 选择 **添加** 。

1. 输入 CA 证书的显示名字。

1. 仅将 CA 证书上传到 IoT 中心。 证书的名称为 `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. 选择“保存”。

### <a name="step-3---prove-possession"></a>步骤 3 — 证明所有权

1. 选择在上述步骤中创建的新 CA 证书。

1. 选择“证书详细信息”对话框中的“生成验证码”。  有关详细信息，请参阅[证明对 CA 证书的所有权](tutorial-x509-prove-possession.md)。

1. 创建包含验证码的证书。 例如，如果验证代码为 `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`，请运行以下代码，在包含主题 `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` 的工作目录 `verification-code.cert.pem` 中创建新证书。

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. 在“证书详细信息”对话框中，将证书上传到 IoT 中心。

1. 选择“验证”  。

### <a name="step-4---create-a-new-device"></a>步骤 4 — 创建新设备

为 IoT 中心创建设备：

1. 在 IoT 中心中，导航到“IoT 设备”部分。

1. 添加 ID 为 `mydevice` 的新设备。

1. 对于身份验证，请选择“X.509 CA 签名”。

1. 运行 `./certGen.sh create_device_certificate mydevice` 创建新的设备证书。 这会在你的工作目录中创建两个名为 `new-device.cert.pem` 和 `new-device.cert.pfx` 的文件。

### <a name="step-5---test-your-device-certificate"></a>步骤 5 — 测试设备证书

转到[测试证书身份验证](tutorial-x509-test-certificate.md)，以确定设备证书是否可在 IoT 中心进行身份验证。 你将需要证书的 PFX 版本 `new-device.cert.pfx`。

### <a name="step-6---cleanup"></a>步骤 6 — 清理

由于 bash 脚本只是在工作目录中创建证书，因此，只需在完成测试后将其删除即可。

## <a name="next-steps"></a>后续步骤

若要测试证书，请转到[测试证书身份验证](tutorial-x509-test-certificate.md) ，以确定证书是否可在 IoT 中心对设备进行身份验证。
