---
title: 自带密钥 (客户托管密钥) 与媒体服务
description: 你可以使用客户托管的密钥 (即，使用媒体服务自带密钥) 。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a56922c972efeb21c188413522bd05f83b74ca12
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681816"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>自带密钥 (客户管理的密钥) 媒体服务

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

创建自己的密钥 (BYOK) 是一种 Azure 范围的计划，可帮助客户将工作负荷转移到云中。 客户管理的密钥使客户能够遵守行业法规遵从性法规并改善服务的租户隔离。 为客户控制加密密钥是一种在 Microsoft 服务中最大程度地减少不必要的访问和控制和构建置信度的方法。

## <a name="keys-and-key-management"></a>密钥和密钥管理

使用媒体服务 2020-05-01 API 时，可以将自己的密钥与媒体服务一起使用。 将为由媒体服务拥有的系统密钥加密的所有帐户创建默认的帐户密钥。 使用自己的密钥时，帐户密钥使用密钥进行加密。 内容密钥使用帐户密钥进行加密。 JobInputHttp url 和对称令牌验证密钥也进行了加密。

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="客户托管密钥替换系统管理的密钥":::

媒体服务使用媒体服务帐户的托管标识从你拥有的 Key Vault 读取你的密钥。 媒体服务要求 Key Vault 与帐户位于同一区域，并且已启用软删除和清除保护。

密钥可以是2048、3072或 4096 RSA 密钥，同时支持 HSM 和软件密钥。

> [!NOTE]
> EC 项不受支持。

你可以指定密钥名称和密钥版本，或仅指定密钥名称。 当你只使用密钥名称时，媒体服务将使用最新的密钥版本。 系统将自动检测新版本的客户密钥，并重新对帐户密钥进行加密。

> [!WARNING]
> 媒体服务监视对客户密钥的访问。 如果客户密钥变为不可访问 (例如，已删除密钥或 Key Vault 已删除或已删除访问授权) ，则媒体服务会将该帐户转换为客户密钥 "无法访问" 状态 (有效禁用该帐户) 。 但是，可以在此状态下删除此帐户。 唯一受支持的操作是帐户获取、列出和删除; (编码、流式传输等) 的所有其他请求将会失败，直到还原对帐户密钥的访问。

## <a name="tutorials"></a>教程

- [使用 Azure 门户通过媒体服务使用客户管理的密钥或 BYOK](tutorial-byok-portal.md)
- [将客户托管的密钥或 BYOK 与 Media Services REST API 一起使用](tutorial-byok-postman.md)。

## <a name="next-steps"></a>后续步骤

[使用媒体服务动态加密保护内容](content-protection-overview.md)
