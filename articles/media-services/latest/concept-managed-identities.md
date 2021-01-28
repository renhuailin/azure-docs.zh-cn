---
title: 托管标识和受信任的存储
description: 可将媒体服务与托管标识结合使用，以实现受信任的存储。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: 291508a6beaa687b3a10f55df4591ce601ab51a0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956168"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>将托管标识和受信任的存储与媒体服务结合使用

可将媒体服务与[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)结合使用，以实现受信任的存储。 创建媒体服务帐户时，必须将其与存储帐户相关联。 媒体服务可以使用系统身份验证来访问该存储帐户。 媒体服务验证媒体服务帐户和存储帐户是否位于同一订阅中，并验证添加关联的用户是否具有使用 Azure 资源管理器 RBAC 访问存储帐户的权限。

## <a name="trusted-storage"></a>受信任的存储

但是，如果想要使用防火墙来保护存储帐户，则必须使用托管标识身份验证。 这使媒体服务可通过受信任的存储访问权限访问配置有防火墙或 VNet 限制的存储帐户。  若要详细了解受信任的 Microsoft 服务，请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

## <a name="media-services-managed-identity-scenarios"></a>媒体服务托管标识场景

目前有两种场景可将托管标识用于媒体服务：

- 使用媒体服务帐户的托管标识来访问存储帐户。

- 使用媒体服务帐户的托管标识访问 Key Vault 以访问客户密钥。

接下来的两个部分介绍这两种场景的差异。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>使用媒体服务帐户的托管标识来访问存储帐户

1. 创建包含托管标识的媒体服务帐户。
1. 授予托管标识主体对你所拥有存储帐户的访问权限。
1. 然后，媒体服务可以使用托管标识代表你访问存储帐户。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>使用媒体服务帐户的托管标识访问 Key Vault 以访问客户密钥

1. 创建包含托管标识的媒体服务帐户。
1. 授予托管标识主体对你所拥有 Key Vault 的访问权限。
1. 将媒体服务帐户配置为使用基于客户密钥的帐户加密。
1. 媒体服务使用托管标识代表你访问 Key Vault。

有关客户管理的密钥和 Key Vault 的详细信息，请参阅[创建自己的密钥（客户管理的密钥）用于媒体服务](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>教程

这些教程包括上述两种场景。

- [使用 Azure 门户将客户管理的密钥或 BYOK 与媒体服务配合使用](tutorial-byok-portal.md)
- [将客户管理的密钥或 BYOK 与媒体服务 REST API 配合使用](tutorial-byok-postman.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解托管标识可以为你和你的 Azure 应用程序执行哪些操作，请参阅 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。