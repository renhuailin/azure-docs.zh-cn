---
title: 媒体服务的受信任存储
description: 托管标识身份验证允许媒体服务通过受信任的存储访问来访问已配置防火墙或 VNet 限制的存储帐户。
keywords: 受信任的存储，托管标识
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408477"
---
# <a name="trusted-storage-for-media-services"></a>媒体服务的受信任存储

创建媒体服务帐户时，必须将其与存储帐户相关联。 媒体服务可以使用系统身份验证来访问该存储帐户。 媒体服务验证媒体服务帐户和存储帐户是否位于同一订阅中，并验证添加关联的用户是否具有使用 Azure 资源管理器 RBAC 访问存储帐户的权限。

但是，如果你想要使用防火墙来保护存储帐户并启用受信任的存储，则必须使用 [托管标识](concept-managed-identities.md) 身份验证。 这使媒体服务可通过受信任的存储访问权限访问配置有防火墙或 VNet 限制的存储帐户。

若要了解用托管标识创建受信任存储的方法，请阅读 [托管标识和媒体服务](concept-managed-identities.md)。

有关客户管理的密钥和 Key Vault 的详细信息，请参阅[创建自己的密钥（客户管理的密钥）用于媒体服务](concept-use-customer-managed-keys-byok.md)

若要详细了解受信任的 Microsoft 服务，请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

## <a name="tutorials"></a>教程

这些教程包括上述两种场景。

- [使用 Azure 门户将客户管理的密钥或 BYOK 与媒体服务配合使用](tutorial-byok-portal.md)
- [将客户管理的密钥或 BYOK 与媒体服务 REST API 配合使用](tutorial-byok-postman.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解托管标识可以为你和你的 Azure 应用程序执行哪些操作，请参阅 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。