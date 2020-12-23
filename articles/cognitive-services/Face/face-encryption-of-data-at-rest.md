---
title: 静态数据的人脸服务加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍适用于人脸的数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 8c6f4f7db312355b719deb434bf6a46fa55eec9d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912780"
---
# <a name="face-service-encryption-of-data-at-rest"></a>静态数据的人脸服务加密

在将数据保存到云时，人脸服务会自动加密数据。 人脸服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户托管的密钥仅可用于 E0 定价层。 若要请求使用客户管理的密钥的功能，请填写并提交 [面部服务 Customer-Managed 密钥请求窗体](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与人脸服务后，你将需要创建新的人脸资源，并选择 E0 作为定价层。 创建具有 E0 定价层的面部资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* 有关支持 CMK 的服务的完整列表，请参阅 [认知服务的客户托管密钥](../encryption/cognitive-services-encryption-keys-portal.md)
* [什么是 Azure Key Vault？](../../key-vault/general/overview.md)
* [认知服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)