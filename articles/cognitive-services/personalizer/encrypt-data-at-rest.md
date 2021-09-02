---
title: 个性化体验创建服务静态数据加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍个性化体验创建服务的静态数据加密，以及如何启用和管理 CMK。
author: jeffmend
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jeffme
ms.openlocfilehash: d6d4aaa9b6a04614fdae4f794da9153a03031727
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829755"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>个性化体验创建服务静态数据加密

个性化体验创建服务在将数据保存到云时会自动加密数据。 个性化体验创建服务加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥仅在 E0 定价层可用。 若要请求使用客户管理的密钥的能力，请填写并提交[个性化体验创建服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)。 你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 获批可在个性化体验创建服务中使用 CMK 后，需要新建个性化体验创建服务资源并选择 E0 作为定价层。 创建具有 E0 定价层的个性化体验创建服务资源后，可以使用 Azure Key Vault 设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* [个性化体验创建服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)