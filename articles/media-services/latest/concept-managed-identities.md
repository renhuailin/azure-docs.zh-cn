---
title: 通过媒体服务管理标识和受信任的存储
description: 媒体服务可与托管标识一起使用，以启用受信任的存储。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: d0811e8f9183ee334d413bcad69f2c7b32023be3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499350"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>通过媒体服务管理标识和受信任的存储

媒体服务可与 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md) 一起使用，以启用受信任的存储。 创建媒体服务帐户时，必须将其与存储帐户相关联。 媒体服务可以使用系统身份验证访问该存储帐户。 媒体服务验证媒体服务帐户和存储帐户是否在同一订阅中，并验证添加关联的用户是否已通过 Azure 资源管理器 RBAC 访问存储帐户。

## <a name="trusted-storage"></a>受信任的存储

但是，如果要使用防火墙来保护存储帐户，则必须使用托管标识身份验证。 它允许 Media Services 使用通过受信任的存储访问配置了防火墙或 VNet 限制的存储帐户。  有关受信任的 Microsoft 服务的详细信息，请参阅 [配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

## <a name="media-services-managed-identity-scenarios"></a>媒体服务托管标识方案

目前有两种方案可将托管标识用于媒体服务：

- 使用媒体服务帐户的托管标识来访问存储帐户。

- 使用媒体服务帐户的托管标识访问用于访问客户密钥的 Key Vault。

接下来的两部分介绍两个方案中的差异。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>使用媒体服务帐户的托管标识来访问存储帐户

1. 创建具有托管标识的媒体服务帐户。
1. 向托管标识主体授予你拥有的存储帐户的访问权限。
1. 然后，媒体服务可以使用托管标识代表您访问存储帐户。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>使用媒体服务帐户的托管标识访问 Key Vault 以访问客户密钥

1. 创建具有托管标识的媒体服务帐户。
1. 向托管标识主体授予对你拥有的 Key Vault 的访问权限。
1. 将媒体服务帐户配置为使用基于客户密钥的帐户加密。
1. 媒体服务使用托管标识以你的名义访问 Key Vault。

有关客户托管密钥和 Key Vault 的详细信息，请参阅 [自带密钥 (客户管理的密钥) 媒体服务](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>教程

这些教程包括上述两个方案。

- [使用 Azure 门户通过媒体服务使用客户管理的密钥或 BYOK](tutorial-byok-portal.md)
- [将客户托管的密钥或 BYOK 与 Media Services REST API 一起使用](tutorial-byok-postman.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解托管标识可为你和 Azure 应用程序执行哪些操作，请参阅 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。