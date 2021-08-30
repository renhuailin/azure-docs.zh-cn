---
title: Azure Stack Edge Pro 的证书要求和疑难解答 | Microsoft Docs
description: 介绍 Azure Stack Edge Pro 设备的证书要求以及如何排查与该设备相关的证书错误。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 6071420216a4c3e365ca5c35e1bf59713974e679
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360535"
---
# <a name="certificate-requirements"></a>证书要求

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文将介绍证书要求，必须满足这些要求，才能将证书安装到 Azure Stack Edge Pro 设备上。 这些要求与 PFX 证书、颁发机构、证书使用者名称和使用者可选名称以及受支持的证书算法相关。

## <a name="certificate-issuing-authority"></a>证书颁发机构

证书颁发要求如下所示：

* 证书必须由内部证书颁发机构或公共证书颁发机构颁发。

* 不支持使用自签名证书。

* 证书的“颁发给:”字段不得与“颁发者:”字段相同，根 CA 证书除外 。


## <a name="certificate-algorithms"></a>证书算法

你的设备仅支持 Rivest–Shamir–Adleman (RSA) 证书。 不支持椭圆曲线数字签名算法 (ECDSA) 证书。

包含 RSA 公钥的证书称为 RSA 证书。 包含椭圆曲线加密 (ECC) 公钥的证书称为 ECDSA（椭圆曲线数字签名算法）证书。

证书算法要求如下所示：

* 证书必须使用 RSA 密钥算法。

* 仅支持提供程序为 Microsoft RSA/Schannel Cryptographic Provider 的 RSA 证书。

* 证书签名算法不能为 SHA1。

* 密钥大小最小为 4096。

## <a name="certificate-subject-name-and-subject-alternative-name"></a>证书使用者名称和使用者可选名称

证书必须满足以下使用者名称和使用者可选名称要求：

* 你可以在证书的使用者可选名称 (SAN) 字段中使用涵盖所有命名空间的单个证书。 也可以为每个命名空间使用单个证书。 这两种方法都要求必要时对终结点使用通配符，例如二进制大型对象 (Blob) 这种情况。

* 确保使用者名称（使用者名称中的常用名称）是使用者可选名称扩展名中使用者可选名称的一部分。

* 你可以使用一个涵盖证书 SAN 字段中所有命名空间的通配符证书。

* 创建终结点证书时使用下表：

    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |Azure 资源管理器|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 存储|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |本地 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |两个终结点的多个 SAN 单一证书|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |节点|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate 采用硬编码。  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX 证书

Azure Stack Edge Pro 设备上安装的 PFX 证书应满足以下要求：

* 从 SSL 颁发机构获取证书时，请确保获取证书的完整签名链。

* 导出 PFX 证书时，请确保已选择“包括证书链中的所有证书(如果可能)”选项。

* 为终结点、本地 UI、节点、VPN 和 Wi-Fi 使用 PFX 证书，因为 Azure Stack Edge Pro 既需要公钥，也需要私钥。 私钥必须设置本地计算机密钥属性。

* 证书的 PFX 加密应当为 3DES。 这是从 Windows 10 客户端或 Windows Server 2016 证书存储导出时使用的默认加密。 如需了解与 3DES 相关的详细信息，请参阅[三重 DES](https://en.wikipedia.org/wiki/Triple_DES)。

* 证书 PFX 文件的“密钥使用”字段中必须具有有效的“数字签名”和“密钥加密”值  。

* 证书 PFX 文件的“增强型密钥使用”字段中必须包含“服务器身份验证(1.3.6.1.5.5.7.3.1)”和“客户端身份验证(1.3.6.1.5.5.7.3.2)”值  。

* 如果使用的是 Azure Stack 准备情况检查器工具，则在部署时所有证书 PFX 文件的密码必须相同。 有关详细信息，请参阅[使用 Azure Stack Hub 准备情况检查器工具为 Azure Stack Edge Pro 创建证书](azure-stack-edge-gpu-create-certificates-tool.md)。

* 证书 PFX 的密码必须是复杂密码。 请记下此密码，因为它将用作部署参数。

* 仅使用提供程序为 Microsoft RSA/Schannel Cryptographic Provider 的 RSA 证书。

有关详细信息，请参阅[导出带私钥的 PFX 证书](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)。

## <a name="next-steps"></a>后续步骤

- 为设备创建证书

    - 通过 [Azure PowerShell cmdlet](azure-stack-edge-gpu-create-certificates-powershell.md)
    - 通过 [Azure Stack Hub 就绪情况检查器工具](azure-stack-edge-gpu-create-certificates-tool.md)。

