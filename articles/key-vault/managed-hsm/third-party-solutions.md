---
title: Azure 密钥保管库管理的 HSM - 第三方解决方案 | Microsoft Docs
description: 了解与托管 HSM 集成的第三方解决方案。
services: key-vault
author: mbaldwin
editor: ''
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: mbaldwin
ms.openlocfilehash: e70afc4a89fc0c9ce5c6ec59cd795b83b5e078b0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443462"
---
# <a name="third-party-solutions"></a>第三方解决方案

Azure Key Vault 托管 HSM 是一项完全托管、高度可用、单租户、符合标准的云服务，通过该服务，可以使用通过 FIPS 140-2 级别 3 验证的 HSM 来保护云应用程序的加密密钥。 [了解详细信息](overview.md)。

一些供应商与 Microsoft 密切合作，将他们的解决方案与托管 HSM 集成。 下表列出了这些解决方案以及简要说明（由供应商提供）。 还提供了 Azure Marketplace 产品/服务和文档的链接。


## <a name="third-party-solutions-integrated-with-managed-hsm"></a>与托管 HSM 集成的第三方解决方案

| 供应商名称 | 解决方案描述 |
|-------------|-------------------------------------------------|
|[Cloudflare](https://cloudflare.com)|Cloudflare 的无密钥 SSL 允许网站使用 Cloudflare 的 SSL 服务，同时在托管 HSM 中保管私钥。 此服务与托管 HSM 相结合，通过保护私钥、在内部执行签名和加密操作、提供访问控制以及将密钥存储在防篡改 FIPS 140-2 3 级 HSM 中，有助于提供高级别的保护。 <br>[文档](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/azure-managed-hsm)
|[NewNet 通信技术](https://newnet.com/)|NewNet 的安全事务云 (STC) 是业界首创的基于云的安全支付路由、交换、传输解决方案，利用基于云的虚拟化 HSM 进行了强化，能够解决移动、网络和店内支付问题。 STC 实现了支付实体的云转型以及绿地支付提供商的快速部署。<br/>[Azure Marketplace offering](https://azuremarketplace.microsoft.com/marketplace/apps/newnetcommunicationtechnologies1589991852134.secure_transaction_cloud?tab=overview)<br/>[文档](https://newnet.com/business-units/secure-transactions/products/secure-transaction-cloud-stc/)|
|[PrimeKey](https://www.primekey.com)|EJBCA Enterprise 是世界上使用最广泛的 PKI（公钥基础设施），为任何用例的可信身份和安全通信提供基本的安全服务。 单个 EJBCA Enterprise 实例支持多个 CA 和级别，让用户能够为多个用例构建完整的基础架构。<br>[Azure Marketplace offering](https://azuremarketplace.microsoft.com/marketplace/apps/primekey.ejbca_enterprise_cloud_2)<br/>[文档](https://doc.primekey.com/x/a4z_/)|



## <a name="next-steps"></a>后续步骤
* [托管 HSM 概述](overview.md)
* [托管 HSM 最佳实践](best-practices.md)

