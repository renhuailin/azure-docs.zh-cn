---
title: 使用 Azure IoT 中心设备预配服务验证 X.509 CA 证书
description: 如何使用 Azure IoT 中心设备预配服务 (DPS) 进行 X.509 CA 证书的所有权证明
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: c1c4490b89a960d89cb1678c96391faf0ac4cc5b
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108509"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>如何使用设备预配服务执行 X.509 CA 证书的所有权证明

验证的 X.509 证书颁发机构 (CA) 证书是已上传并注册到预配服务，且已在该服务中完成所有权证明的 CA 证书。 

使用登记组时，验证的证书将发挥重要作用。 验证证书所有权可以确保证书上传者拥有该证书的私钥，从而提供一个附加的安全层。 验证可以防止恶意行动者提取中间证书，并使用该证书在其自己的预配服务中创建登记组，从而有效地劫持你的设备，因此，可防止他们探查你的流量。 通过证明证书链中根证书或中间证书的所有权，可以证明你有权针对需要注册为登记组一部分的设备生成叶证书。 出于此原因，在登记组中配置的根证书或中间证书必须是验证的证书，或者在服务中进行身份验证时，必须滚动更新为设备所代表的证书链中的某个已验证证书。 若要详细了解 X.509 证书证明，请参阅 [X.509 证书](concepts-x509-attestation.md)和[使用 X.509 证书控制设备对预配服务的访问](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)。

## <a name="automatic-verification-of-intermediate-or-root-ca-through-self-attestation"></a>通过自我证明自动验证中间 CA 或根 CA
如果你使用的是你信任的中间 CA 或根 CA，并且知道你拥有该证书的完全所有权，则可以自我证明你已验证该证书。

若要添加自动验证的证书，请按照以下步骤进行操作：

1. 在 Azure 门户中导航到预配服务，从左侧菜单打开“证书”。  
2. 单击“添加”以添加新证书。 
3. 输入证书的友好显示名称。 浏览到表示 X.509 证书公共部分的 .cer 或 .pem 文件。 单击“上载” 。 
4. 选中“在上传时将证书状态设置为已验证”旁边的复选框。

    ![上传 certificate_with_verified](./media/how-to-verify-certificates/add-certificate-with-verified.png)

1. 单击“保存”。
1. 此时证书显示在证书选项卡中，状态为“已验证”。
  
    ![Certificate_Status](./media/how-to-verify-certificates/certificate-status.png)

## <a name="manual-verification-of-intermediate-or-root-ca"></a>手动验证中间 CA 或根 CA

所有权证明涉及以下步骤：
1. 获取预配服务针对 X.509 CA 证书生成的唯一验证码。 可以从 Azure 门户获取此信息。
2. 使用验证码作为使用者创建 X.509 验证证书，使用与 X.509 CA 证书关联的私钥对该证书进行签名。
3. 将签名的验证证书上传到服务。 服务将使用要验证的 CA 证书的公共部分来验证验证证书，从而证明你拥有 CA 证书的私钥。


### <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>注册 X.509 证书的公共部分并获取验证码

若要将 CA 证书注册到预配服务并获取可在所有权证明期间使用的验证码，请遵循以下步骤。 

1. 在 Azure 门户中导航到预配服务，从左侧菜单打开“证书”。  
2. 单击“添加”以添加新证书。 
3. 输入证书的友好显示名称。 浏览到表示 X.509 证书公共部分的 .cer 或 .pem 文件。 单击“上载” 。 
4. 收到已成功上传证书的通知后，单击“保存”。 

    ![上传证书](./media/how-to-verify-certificates/add-new-cert.png)  

   该证书将显示在“证书资源管理器”列表中。  请注意，此证书的“状态”为“未验证”。  

5. 单击在上一步骤中添加的证书。

6. 在“证书详细信息”中，单击“生成验证码”。  

7. 预配服务会创建一个可用于验证证书所有权的 **验证码**。 将此代码复制到剪贴板。 

   ![验证证书](./media/how-to-verify-certificates/verify-cert.png)  

### <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>对验证代码进行数字签名，以创建验证证书

现在，需要使用与 X.509 CA 证书关联的、可生成签名的私钥来为 *验证码* 签名。 此过程称为[所有权证明](https://tools.ietf.org/html/rfc5280#section-3.1)，最终会生成一个签名的验证证书。

Microsoft 提供了工具和示例来帮助你创建签名的验证证书： 

- **Azure IoT 中心 C SDK** 提供了 PowerShell (Windows) 和 (Linux) Bash 脚本来帮助你创建用于开发的 CA 证书和叶证书，以及使用验证码执行所有权证明。 可将系统相关的[文件](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)下载到工作文件夹，并遵照[管理 CA 证书自述文件](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的说明执行 CA 证书的所有权证明。 
- **Azure IoT 中心 C# SDK** 包含 [组证书验证示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)，可用于执行所有权证明。
 
> [!IMPORTANT]
> 除了执行所有权证明以外，使用上述 PowerShell 和 Bash 脚本还可创建根证书、中间证书和叶证书用于验证和预配设备。 只能在开发中使用这些证书。 切勿将它们用于生产环境。 

文档和 SDK 中提供的 PowerShell 与 Bash 脚本依赖于 [OpenSSL](https://www.openssl.org/)。 还可以借助 OpenSSL 或其他第三方工具执行所有权证明。 有关使用随 SDK 提供的工具的示例，请参阅[创建 X.509 证书链](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)。 


### <a name="upload-the-signed-verification-certificate"></a>上传已签名的验证证书

1. 在门户中将生成的签名作为验证证书上传到预配服务。 在 Azure 门户上的“证书详细信息”中，使用“验证证书 .pem 或 .cer 文件”字段旁边的“文件资源管理器”图标从系统上传已签名的验证证书。   

2. 成功上传证书后，单击“验证”。  在“证书资源管理器”列表中，证书的“状态”会更改为“已验证”。    如果状态未自动更新，请单击“刷新”。 

   ![上传证书验证](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用门户创建登记组，请参阅[使用 Azure 门户管理设备登记](how-to-manage-enrollments.md)。
- 若要了解如何使用服务 SDK 创建登记组，请参阅[使用服务 SDK 管理设备登记](./quick-enroll-device-x509-java.md)。
