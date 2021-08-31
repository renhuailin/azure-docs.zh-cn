---
title: 通过 Azure PowerShell 创建 Azure Stack Edge Pro GPU 证书 | Microsoft Docs
description: 介绍如何使用 Azure PowerShell cmdlet 创建 Azure Stack Edge Pro GPU 设备的证书。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: fc7ec5574e0f0223a66bc4e42ce9029db0ae4fc6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364013"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上使用证书

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍使用 Azure PowerShell cmdlet 创建自己的证书的过程。 本文包含用户打算在 Azure Stack Edge 设备上使用自己的证书时，需要遵循的准则。

证书确保设备与访问设备的客户端之间的通信是受信任的，并且你向正确的服务器发送加密的信息。 最初配置 Azure Stack Edge 设备时，将自动生成自签名证书。 你也可以选择自带证书。 

你可以使用下列方法之一，为设备创建你自己的证书：

 - 使用 Azure PowerShell cmdlet。
 - 使用 Azure Stack Hub 就绪情况检查器工具来创建证书签名请求 (CSR)，以帮助你的证书颁发机构颁发证书。 

本文只介绍如何使用 Azure PowerShell cmdlet 创建自己的证书。 

## <a name="prerequisites"></a>先决条件

在自带证书之前，请确保：

- 熟悉[可与 Azure Stack Edge 设备一起使用的证书类型](azure-stack-edge-gpu-certificates-overview.md)。
- 已查看[每种类型的证书的证书要求](azure-stack-edge-gpu-certificate-requirements.md)。


## <a name="create-certificates"></a>创建证书

以下部分介绍了创建签名链和终结点证书的过程。


### <a name="certificate-workflow"></a>证书工作流

你可以使用定义的方式为环境中运行的设备创建证书。 可以使用 IT 管理员提供的证书。 

对于仅开发或测试目的，还可以使用 Windows PowerShell 在本地系统上创建证书。 为客户端创建证书时，请遵循以下准则：

1. 你可以创建以下任何类型的证书：

    - 创建一个有效证书，与一个完全限定的域名 (FQDN) 一起使用。 例如，mydomain.com。
    - 还可以创建通配符证书来保护主域名和多个子域。 例如，.mydomain.com。
    - 创建使用者可选名称 (SAN) 证书，它将在一个证书中覆盖多个域名。 

2. 如果自带证书，则需要签名链的根证书。 有关步骤，请参阅[创建签名链证书](#create-signing-chain-certificate)。

3. 接下来，可以为设备、blob 和 Azure 资源管理器的本地 UI 创建终结点证书。 可以为设备、blob 和 Azure 资源管理器创建 3 个单独的证书，也可以为所有 3 个终结点创建一个证书。 有关详细步骤，请参阅[创建签名和终结点证书](#create-signed-endpoint-certificates)。

4. 无论是创建 3 个单独的证书还是一个证书，请根据为每个证书类型提供的指导，指定使用者名称 (SN) 和使用者可选名称 (SAN)。 

### <a name="create-signing-chain-certificate"></a>创建签名链证书

通过在管理员模式下运行的 Windows PowerShell 创建这些证书。 以这种方式创建的证书应只用于开发或测试目的。

签名链证书只需创建一次。 其他终结点证书将引用此证书进行签名。
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>创建签名的终结点证书

通过在管理员模式下运行的 Windows PowerShell 创建这些证书。

在这些示例中，使用以下项为设备创建终结点证书：
    - 设备名称：`DBE-HWDC1T2`
    - DNS 域：`microsoftdatabox.com`

替换为设备的名称和 DNS 域，以便为设备创建证书。
 
Blob 终结点证书

为个人存储中的 Blob 终结点创建证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Azure 资源管理器终结点证书

为个人存储中的 Azure 资源管理器终结点创建证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

设备本地 Web UI 证书

为个人存储中设备的本地 Web UI 创建证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

所有终结点的单个多 SAN 证书

为个人存储中的所有终结点创建单个证书。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

创建证书后，下一步是在 Azure Stack Edge Pro GPU 设备上上传证书。

## <a name="next-steps"></a>后续步骤

[在设备上上传证书](azure-stack-edge-gpu-manage-certificates.md)。
