---
title: Azure Active Directory 证书颁发机构
description: Azure 中使用的受信任证书的列表
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96859125"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Azure Active Directory 使用的证书颁发机构

> [!IMPORTANT]
> 此页中的信息仅与用于显式指定可接受证书颁发机构 (CA) 列表的实体相关。 除非没有其他选择，否则应该避免这种被称为证书固定的做法。

尝试通过 TLS/SSL 协议访问 Azure Active Directory (Azure AD) 标识服务的任何实体都将获得下列 CA 提供的证书。 如果该实体信任那些 CA，它可以使用这些证书来验证标识和标识服务的合法性并建立安全连接。

证书颁发机构可以划分为根 CA 和中间 CA。 通常，根 CA 具有一个或多个关联的中间 CA。 本文列出了 Azure AD 标识服务使用的根 CA 以及其中每一个根 CA 所关联的中间 CA。 对于每个 CA，我们都会提供统一资源标识符 (URI)，以用于下载关联的颁发机构信息访问 (AIA) 文件和证书吊销列表分发点 (CDP) 文件。 在适当的时候，我们还会提供联机证书状态协议 (OCSP) 终结点的 URI。

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Azure 公共云和 Azure 美国政府云中使用的 CA

不同的服务可以使用不同的根或中间 CA。 因此，可能需要下面列出的所有条目。

### <a name="digicert-global-root-g2"></a>DigiCert 全局根 G2


| 根 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - |- |-|-|-|-|
| DigiCert 全局根 G2| 033af1e6a711a 9a0bb2864b11d09fae5| 2013 年 8 月 1 日 <br>2038 年 1 月 15 日| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>关联的中间 CA

| 颁发 CA 和中间 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - | 
| Microsoft Azure TLS 颁发 CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 2020 年 7 月 29 日<br>2024 年 6 月 27 日| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS 颁发 CA 02| 0c6ae97cced59983 8690a00a9ea53214| 2020 年 7 月 29 日<br>2024 年 6 月 27 日| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS 颁发 CA 03| 0d7bede97d8209967a 52631b8bdd18bd| 2020 年 7 月 29 日<br>2024 年 6 月 27 日| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS 颁发 CA 06| 02e79171fb8021e93fe 2d983834c50c0| 2020 年 7 月 29 日<br>2024 年 6 月 27 日| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust 根

| 根 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - | 
| Baltimore CyberTrust 根| 020000b9| 2000 年 5 月 12 日<br>2025 年 5 月 12 日| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>关联的中间 CA

| 颁发 CA 和中间 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 2020 年 7 月 21 日<br>2024 年 10 月 8 日| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 2020 年 7 月 21 日<br>2024 年 5 月 20 日| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert 全局根 CA

| 根 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - | 
| DigiCert 全局根 CA| 083be056904246 b1a1756ac95991c74a| 2006 年 11 月 9 日<br>2031 年 11 月 9 日| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>关联的中间 CA

| 颁发 CA 和中间 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - |
| DigiCert SHA2 安全服务器 CA| 01fda3eb6eca75c 888438b724bcfbc91| 2013 年 3 月 8 日，2023 年 3 月 8 日| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 安全服务器 CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |2020 年 9 月 22 日<br>2030 年 9 月 22 日|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Azure 中国世纪互联云中使用的 CA

### <a name="digicert-global-root-ca"></a>DigiCert 全局根 CA


| 根 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - |
| DigiCert 全局根 CA| 083be056904246b 1a1756ac95991c74a| 2006 年 11 月 9 日<br>2031 年 11 月 9 日| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>关联的中间 CA

| 颁发 CA 和中间 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - | - |
| DigiCert 基本 RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 2020 年 3 月 4 日<br>2030 年 3 月 4 日| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>后续步骤
[了解 Microsoft 365 加密链](/microsoft-365/compliance/encryption-office-365-certificate-chains)
