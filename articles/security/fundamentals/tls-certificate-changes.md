---
title: Azure TLS 证书更改
description: Azure TLS 证书更改
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 09/13/2021
ms.author: mbaldwin
ms.openlocfilehash: 01acde63371fea6a8c3f752caf31fa883e5e617e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641211"
---
# <a name="azure-tls-certificate-changes"></a>Azure TLS 证书更改  

Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 此更改正在进行中，因为当前 CA 证书不符合某个 CA/浏览器论坛基线要求，并且将于 2021 年 2 月 15 日撤销。

## <a name="when-will-this-change-happen"></a>此更改将在何时进行？

从 2020 年 8 月 13 日起，现有 Azure 终结点已开始分阶段转换。 所有新创建的 Azure TLS/SSL 终结点都包含链接到新根 CA 的已更新证书。

此更改会影响所有 Azure 服务。 下面是特定服务的一些其他详细信息：

- [Azure Active Directory](../../active-directory/index.yml) (Azure AD) 服务在 2020 年 7 月 7 日开始此过渡。
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub)和 [DPS](../../iot-dps/index.yml) 将继续使用 Baltimore CyberTrust 根 CA，但其中间 CA 将会更改。 [单击此处了解详细信息](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456)。
- 对于 [Azure 存储](../../storage/index.yml)，[请单击此处获取详细信息](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-critical-changes-are-almost-here-and-why-you/ba-p/2741581)。
- [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) 将继续使用 Baltimore CyberTrust 根 CA，但其中间 CA 将会更改。 [单击此处了解详细信息](../../azure-cache-for-redis/cache-whats-new.md)。
- Azure 实例元数据服务将继续使用 Baltimore CyberTrust 根 CA，但其中间 CA 将会更改。 [单击此处了解详细信息](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html)。

> [!IMPORTANT]
> 进行此更改之后，客户可能需要更新其应用程序，以防止在尝试连接到 Azure 存储时出现连接故障。
https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-critical-changes-are-almost-here-and-why-you/ba-p/2741581
## <a name="what-is-changing"></a>有什么变化？

如今，Azure 服务使用的大多数 TLS 证书都链接到以下根 CA：

| CA 的公用名 | 指纹 (SHA1) |
|--|--|
| [Baltimore CyberTrust 根](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure 服务使用的 TLS 证书都链接到以下根 CA 之一：

| CA 的公用名 | 指纹 (SHA1) |
|--|--|
| [DigiCert 全局根 G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert 全局根 CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust 根](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST 根类 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA 根证书颁发机构 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC 根证书颁发机构 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>何时可以停用旧的中间指纹？

在 2021 年 2 月 15 日 之前，不会撤销当前 CA 证书。 在该日期后，可以从代码中删除旧指纹。

如果此日期发生更改，则会收到新的吊销日期通知。

## <a name="will-this-change-affect-me"></a>此更改将对我产生影响吗？ 

我们预计 **大多数 Azure 客户不会** 受到影响。  但是，如果应用程序显式指定可接受 CA 的列表，则可能会受到影响。 这种做法称为证书固定。

下面是检测应用程序是否受影响的一些方式：

- 在源代码中搜索在[此处](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到的任何 Microsoft IT TLS CAs 的指纹、公用名和其他证书属性。 如果存在匹配，则应用程序会受到影响。 若要解决此问题，请更新源代码，包括新的 CA。 最佳做法是确保在简短通知时可添加或编辑 CA。 行业法规要求在 7 天内替换 CA 证书，因此依赖于固定的客户需要迅速做出反应。

- 如果你的应用程序与 Azure API 或其他 Azure 务集成，并且你不确定它是否使用证书固定，请向应用程序供应商核实。

- 与 Azure 服务进行通信的不同操作系统和语言运行时可能需要额外步骤，才能正确利用这些新的根来构建证书链：
    - Linux：许多发行版要求将 CA 添加到 /etc/ssl/certs。 有关特定说明，请查看发行版的相应文档。
    - **Java**：确保 Java 密钥存储包含上面列出的 CA。
    - 在断开连接的环境中运行的 Windows：在断开连接的环境中运行的系统需要将新根添加到受信任的根证书颁发机构存储，并将中间证书添加到中间证书颁发机构存储。
    - Android：查看适用于你设备和 Android 版本的文档。
    - 其他硬件设备（尤其是 IoT）：联系设备制造商。

- 如果环境中的防火墙规则设置为仅允许对特定证书吊销列表 (CRL) 下载和/或在线证书状态协议 (OCSP) 验证位置进行出站呼叫。 需要允许以下 CRL 和 OCSP URL：

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46;microsoft&#46;com/pkiops

## <a name="next-steps"></a>后续步骤

如果有其他问题，请通过[支持](https://azure.microsoft.com/support/options/)联系我们。
