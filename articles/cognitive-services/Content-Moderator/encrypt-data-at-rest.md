---
title: 内容审查器的静态数据加密
titleSuffix: Azure Cognitive Services
description: 内容审查器的静态数据加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 7beb615307b602789b845e10cee28ca402acf9e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217175"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>内容审查器的静态数据加密

将数据持久保存到云中时，内容审查器会自动对其加密，这有助于实现组织的安全性和合规性目标。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户管理的密钥仅在 E0 定价层可用。 若要请求使用客户管理的密钥的权限，请填写并提交[内容审查器客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)。 你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 获批可在内容审查器服务中使用 CMK 后，需要新建内容审查器资源并选择 E0 作为定价层。 创建具有 E0 定价层的内容审查器资源后，可以使用 Azure Key Vault 设置托管标识。

所有 Azure 区域均可使用客户管理的密钥。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>为内容审查器团队启用数据加密

若要为内容审查器评审团队启用数据加密，请参阅[快速入门：在 Web 上试用内容审查器](quick-start.md#create-a-review-team)。  

> [!NOTE]
> 你将需要为内容审查器 E0 定价层提供一个资源 ID。

## <a name="next-steps"></a>后续步骤

* 有关支持 CMK 的服务的完整列表，请参阅[认知服务的客户管理的密钥](../encryption/cognitive-services-encryption-keys-portal.md)
* [什么是 Azure Key Vault？](../../key-vault/general/overview.md)
* [认知服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)