---
title: 人脸服务静态数据的加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍人脸的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a46253e586aaf90a85bc10e2c62ab6eb238f2ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100650807"
---
# <a name="face-service-encryption-of-data-at-rest"></a>人脸服务静态数据的加密

人脸服务在将数据保存到云时会自动加密数据。 人脸服务加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥仅在 E0 定价层可用。 若要请求使用客户管理的密钥的能力，请填写并提交[人脸服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)。 你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 获批可在人脸服务中使用 CMK 后，需要新建人脸资源并选择 E0 作为定价层。 创建具有 E0 定价层的人脸资源后，可以使用 Azure Key Vault 设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* 有关支持 CMK 的服务的完整列表，请参阅[认知服务的客户管理的密钥](../encryption/cognitive-services-encryption-keys-portal.md)
* [什么是 Azure Key Vault？](../../key-vault/general/overview.md)
* [认知服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)
