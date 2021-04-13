---
title: 自定义视觉静态数据加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍自定义视觉的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652252"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>自定义视觉静态数据加密

Azure 自定义视觉在将数据保存到云时会自动加密数据。 自定义视觉加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥只适用于在 2020 年 5 月 11 日以后创建的资源。 若要将 CMK 与自定义视觉配合使用，需要创建新的自定义视觉资源。 在创建资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* 有关支持 CMK 的服务的完整列表，请参阅[认知服务的客户管理的密钥](../encryption/cognitive-services-encryption-keys-portal.md)
* [什么是 Azure Key Vault？](../../key-vault/general/overview.md)
* [认知服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)