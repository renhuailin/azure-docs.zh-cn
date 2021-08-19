---
title: 表单识别器服务静态数据加密
titleSuffix: Azure Applied AI Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍表单识别器的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: fad7440c82ee22a370bfecc13c60f3d4d4828b07
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326251"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>表单识别器静态数据加密

Azure 表单识别器在将数据保存到云时会自动对其进行加密。 表单识别器加密可保护数据，并帮助你履行组织的安全性和合规性承诺。

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥只适用于在 2020 年 5 月 11 日以后创建的资源。 若要将 CMK 与表单识别器结合使用，需要创建新的表单识别器资源。 在创建资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../../cognitive-services/includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* [表单识别器客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)