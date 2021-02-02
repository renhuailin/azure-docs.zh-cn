---
title: '自带密钥 (客户托管密钥) '
description: 可将客户管理的密钥（即自带密钥）与媒体服务配合使用。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 7e58daf72522071eb7d0031d4ea236a21c6d8bfb
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252315"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>自带密钥（客户管理的密钥）与媒体服务

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

自带密钥 (BYOK) 是一种 Azure 范围的计划，可帮助客户将其工作负载转移到云中。 客户管理的密钥使客户能够遵守行业合规性法规并改进服务的租户隔离。 为客户控制加密密钥是一种在 Microsoft 服务中最大程度地减少不必要的访问和控制和构建置信度的方法。

## <a name="keys-and-key-management"></a>密钥和密钥管理

使用媒体服务 2020-05-01 API 时，可以将自己的密钥与媒体服务一起使用。 将为所有帐户创建一个默认帐户密钥，该密钥由媒体服务拥有的系统密钥加密。 你使用自己的密钥时，帐户密钥使用你的密钥进行加密。 内容密钥由帐户密钥加密。 JobInputHttp url 和对称令牌验证密钥也进行了加密。

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="客户管理的密钥替换系统管理的密钥":::

媒体服务使用媒体服务帐户的托管标识从你拥有的 Key Vault 读取密钥。 媒体服务要求 Key Vault 位于帐户所在区域，并且已启用软删除和清除保护。

密钥可以是2048、3072或 4096 RSA 密钥，同时支持 HSM 和软件密钥。

> [!NOTE]
> 不支持 EC 密钥。

可以指定密钥名称和密钥版本，或仅指定密钥名称。 当你仅使用密钥名称时，媒体服务将使用最新的密钥版本。 系统将自动检测新版本的客户密钥，并重新对帐户密钥进行加密。

> [!WARNING]
> 媒体服务监视对客户密钥的访问。 如果客户密钥变为不可访问（例如，已删除密钥或已删除 Key Vault 或已删除访问授权），则媒体服务会将该帐户转换为客户密钥不可访问状态（有效地禁用帐户）。 但是，可以在此状态下删除此帐户。 唯一受支持的操作是帐户 GET、LIST 和 DELETE；在恢复对帐户密钥的访问之前，所有其他请求（编码、流式处理等）都将失败。

## <a name="double-encryption"></a>双重加密

媒体服务支持双重加密。  若要详细了解双重加密，请参阅 [Azure 双重加密](../../security/fundamentals/double-encryption.md)。

在 Media Services 帐户上自动启用双加密。 但是，你需要分别在存储帐户上配置客户托管的密钥和双加密。

## <a name="tutorials"></a>教程

- [使用 Azure 门户将客户管理的密钥或 BYOK 与媒体服务配合使用](tutorial-byok-portal.md)
- [将客户管理的密钥或 BYOK 与媒体服务 REST API 配合使用](tutorial-byok-postman.md)。

## <a name="next-steps"></a>后续步骤

[使用媒体服务动态加密保护内容](content-protection-overview.md)
