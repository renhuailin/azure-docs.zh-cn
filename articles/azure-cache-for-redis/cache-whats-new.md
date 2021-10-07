---
title: Azure Cache for Redis 的新增功能
description: Azure Cache for Redis 的最近更新
author: curib
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: cauribeg
ms.openlocfilehash: 2ab3a60a4ce5034bc2c0b389d6a541eed6594671
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534601"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Azure Cache for Redis 的新增功能

## <a name="azure-tls-certificate-change"></a>Azure TLS 证书更改

Microsoft 在将 Azure 服务更新为使用来自一组不同的证书颁发机构 (CA) 的 TLS 服务器证书。 这一更改从 2020 年 8 月 13 日至 2020 年 10 月 26 日（预计）分阶段推出。 Azure 进行此更改是因为[当前 CA 证书不符合某个 CA/浏览器论坛基线要求](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)。 此问题已于 2020 年 7 月 1 日报告，适用于全球多个热门公钥基础结构 (PKI) 提供商。 目前，Azure 服务使用的大多数 TLS 证书来自 Baltimore CyberTrust 根 PKI。 Azure Cache for Redis 服务将继续链接到 Baltimore CyberTrust 根。 不过从 2020 年 10 月 12 日开始，其 TLS 服务器证书将由新的中间证书颁发机构 (ICA) 颁发。

> [!NOTE]
> 此更改仅限于公共 [Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/)中的服务。 它不包括主权（例如中国）云或政府云。
>
>

### <a name="does-this-change-affect-me"></a>此更改是否会对我产生影响？

我们希望大多数 Azure Cache for Redis 客户不会受此更改的影响。 如果应用程序显式指定可接受证书的列表（做法称为“证书固定”），则可能会受到影响。 如果已固定到中间证书或叶证书，而不是 CyberTrust 根证书，则应该立即采取措施来更改证书配置。

下表提供了有关正在被回滚的证书的信息。 根据应用程序使用的证书，你可能需要对其进行更新，以防丢失与 Azure Cache for Redis 实例的连接。

| CA 类型 | 当前 | 后期滚动（2020 年 10 月 12 日） | 操作 |
| ----- | ----- | ----- | ----- |
| Root | 指纹：d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 有效期：2025 年 5 月 12 日星期一下午 4:59:00<br><br> 使用者名称：<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | 未更改 | 无 |
| 中间 | 指纹：<br> CN = Microsoft IT TLS CA 1<br> 指纹：417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> 指纹：54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> 指纹：8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> 指纹：Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 有效期：2024 年 5 月 20 日星期五凌晨 5:52:38<br><br> 使用者名称：<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | 指纹：<br> CN = Microsoft RSA TLS CA 01<br> 指纹：703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 指纹：b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 有效期：2024 年 10 月 8 日星期二中午 12:00:00<br><br> 使用者名称：<br> O = Microsoft Corporation<br> C = US<br> | 必须 |

### <a name="what-actions-should-i-take"></a>我应该采取什么措施？

如果你的应用程序使用操作系统证书存储或将 Baltimore 根固定在其他地方，则无需执行任何操作。 

如果应用程序固定任何中间或叶 TLS 证书，建议固定以下根：

| 证书 | Thumbprint |
| ----- | ----- |
| [Baltimore 根 CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA 根证书颁发机构 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [DigiCert 全局根 G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 中间证书和叶证书预计会经常更改。 建议不要依赖它们。 请将应用程序固定到根证书，因为它的滚动频率较低。
>
>

若要继续固定中间证书，请将以下内容添加到包含几个附加证书的“固定的中间证书”列表中，以最大程度地减少将来的更改：

| CA 的公用名 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS 颁发 CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS 颁发 CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS 颁发 CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS 颁发 CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

如果应用程序使用代码来验证证书，则需修改代码以识别新固定的证书的属性（例如颁发者、指纹）。 这一额外验证应覆盖所有固定的证书，以便更好地面向未来。

## <a name="next-steps"></a>后续步骤

如果有更多问题，请通过[客户支持](https://azure.microsoft.com/support/options/)联系我们。  
