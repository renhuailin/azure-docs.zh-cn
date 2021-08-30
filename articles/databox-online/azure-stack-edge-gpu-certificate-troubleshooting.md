---
title: 排查带 GPU 的 Azure Stack Edge Pro 的证书问题 | Microsoft Docs
description: 介绍如何排查 Azure Stack Edge Pro GPU 设备的证书错误。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: b5a607b9c0a64c6872ab511f13034e9d6eeb86d7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360498"
---
# <a name="troubleshooting-certificate-errors"></a>排查证书错误

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查在将证书安装到 Azure Stack Edge Pro 设备时的常见证书错误。

## <a name="common-certificate-errors"></a>常见证书错误

下表显示了常见的证书错误以及这些错误的详细信息和可能的解决方案：

> [!NOTE]
> 出现“{0}、{1}、...、{n}”则表示位置参数。 位置参数的值取决于你所使用的证书。

| 错误代码 | 说明 |
|---|---|
| CertificateManagement_UntrustedCertificate | 使用者名称为 {0} 证书的证书链已断开。 上传此证书前，请先上传签名链证书。|
| CertificateManagement_DeviceNotRegistered| 你的设备未激活。 只有激活后才能上传支持证书。|
| CertificateManagement_ExpiredCertificate | 类型为 {0} 的证书已过期或即将过期。 请检查证书到期日期；如果需要，请提供新的证书。|
| CertificateManagement_FormatMismatch | 证书格式不受支持。 请检查证书格式；如果需要，请提供新的证书。  应为 {0}，但找到的是 {1}。 |
| CertificateManagement_GenericError | 无法执行证书管理操作。 请在几分钟后重试此操作。 如果问题持续出现，请联系 Microsoft 支持。 |
| CertificateManagement_HttpsBindingFailure | 使用者名称为 {0} 的证书未能创建安全 HTTPS 通道。 请检查已上传的证书；如果需要，请提供新的证书。 设备节点证书发生此错误。|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | 使用者名称为 {0} 的证书不得有“证书签名”这一密钥用法。 请检查证书的密钥用法；如果需要，请提供新的证书。 签名链证书发生此错误。 |
| CertificateManagement_IncorrectKeyNumber | 使用者名称为 {0} 的证书有不正确的密钥编号 {1}。 请检查证书的密钥编号；如果需要，请提供新的证书。|
| CertificateManagement_InvalidP7b | 上传的证书文件无效。  请检查证书格式；如果需要，请提供新的证书。|
| CertificateManagement_KeyAlgorithmNotRSA | 此证书没有使用 RSA 密钥算法。 只支持 RSA 证书。 |
| CertificateManagement_KeySizeNotSufficient | 使用者名称为 {0} 的证书的密钥大小 {1} 不够大。 密钥大小下限为 4096。|
| CertificateManagement_MissingClientOid | 使用者名称为 {0} 的证书没有客户端身份验证 OID。 请检查你的证书属性；如果需要，请提供新的证书。|
| CertificateManagement_MissingDigitalSignatureKeyUsage | 使用者名称为 {0} 的证书的“密钥用法”中没有“数字签名”。 请检查你的证书属性；如果需要，请提供新的证书。 |
| CertificateManagement_MissingKeyCertSignKeyUsage | 使用者名称为 {0} 的证书的“密钥用法”中没有“证书签名”。 请检查你的证书属性；如果需要，请提供新的证书。|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | 使用者名称为 {0} 的证书的“密钥用法”中没有“密钥加密”。 请检查你的证书属性；如果需要，请提供新的证书。 |
| CertificateManagement_MissingServerOid | 使用者名称为 {0} 的证书没有服务器身份验证 OID。 请检查你的证书属性；如果需要，请提供新的证书。|
| CertificateManagement_NameMismatch | 证书类型不一致。 范围应为 {0}，但找到的是 {1}。 请上传适当的证书。|
| CertificateManagement_NoPrivateKeyPresent | 使用者名称为 {0} 的证书没有私钥。 请上传带私钥的 .pfx 证书。|
| CertificateManagement_NoRSACryptoPrivateKey | 无法访问使用者名称为 {0} 的证书的私钥。 请确保你使用的是受支持的证书。 仅支持 Microsoft RSA/Schannel 加密提供程序。 |
| CertificateManagement_NotSelfSignedCertificate | 使用者名称为 {0} 的证书不是自签名证书。 根证书应为自签名证书 |
| CertificateManagement_NotSupportedOnVirtualAppliance | 不支持在虚拟设备上执行此操作。 此错误表示仅在 Tactical Cloud Appliance 中运行 Data Box Gateway 时才进行签名。 通过 Windows PowerShell 管理设备时出现此错误。|
| CertificateManagement_SelfSignedCertificate | 使用者名称为 {0} 的证书是自签名证书。 请上传正确签名的证书。|
| CertificateManagement_SignatureAlgorithmSha1 | 使用者名称为 {0} 的证书有不受支持的签名算法。 不支持 SHA1-RSA。 |
| CertificateManagement_SubjectNamesInvalid | 使用者名称为 {0} 的证书没有 {1} 证书的正确使用者名称或使用者可选名称。 请检查已上传的证书；如果需要，请提供新的证书。 还应检查 DNS 名称以匹配 SANS 名称。|
| CertificateManagement_UnreadableCertificate | 无法读取类型为 {0} 的证书。 当证书不可读或损坏时，会发生此错误。 提供新证书。|
| CertificateSubjectNotFound | 找不到使用者名称为 {0} 的证书。 提供新证书。|
| CertificateRotationGenericFailure | 无法轮换一个或多个证书。 请过几分钟后再重试。 如果问题持续出现，请联系 Microsoft 支持。|
| CertificateImportFailure | 未在节点 {1} 上导入带有指纹 {0} 的证书。 如果问题持续出现，请联系 Microsoft 支持。 |
| CertificateApplyFailure | 未在节点 {1} 上应用带有指纹 {0} 的证书。 如果问题持续出现，请联系 Microsoft 支持。|
| NodeNotReachable | 无法验证 {0} 上的证书。 请检查系统硬件和软件运行状况。|


## <a name="next-steps"></a>后续步骤

- 审阅[证书要求](azure-stack-edge-gpu-certificate-requirements.md)。
- [使用设备日志和诊断测试进行故障排除](azure-stack-edge-gpu-troubleshoot.md)。
