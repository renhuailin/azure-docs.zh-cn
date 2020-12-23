---
title: 表单识别器静态数据的服务加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍窗体识别器的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 7a8b331c1295ed19afa64e95318bfa14414e6d9f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913052"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>格式识别静态数据的加密

将数据保存到云时，Azure 表单识别器会自动加密数据。 表单识别器加密可保护你的数据，以帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 仅限11月 11 2020 日之后创建的客户托管密钥是可用的资源。 若要将 CMK 与窗体识别器结合使用，需要创建新的窗体识别器资源。 创建资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* [表单识别器 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)