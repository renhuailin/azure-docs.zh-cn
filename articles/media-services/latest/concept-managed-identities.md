---
title: 托管标识
description: 媒体服务可与 Azure 托管标识结合使用。
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258433"
---
# <a name="managed-identities"></a>托管标识

开发人员面临的一个共同挑战是如何管理密码和凭据，以确保不同服务之间的通信安全。 在 Azure 上，托管标识可为 Azure AD 中的 Azure 资源提供标识并使用它来获取 Azure Active Directory (Azure AD) 令牌，从而使开发人员无需管理凭据。

目前有以下两种方案可将托管标识与媒体服务结合使用：

- 使用媒体服务帐户的托管标识来访问存储帐户。

- 使用媒体服务帐户的托管标识访问 Key Vault 以访问客户密钥。

以下两部分介绍这两种方案的步骤。

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>使用媒体服务帐户的托管标识来访问存储帐户

1. 创建包含托管标识的媒体服务帐户。
1. 授予托管标识主体对你所拥有存储帐户的访问权限。
1. 然后媒体服务可以使用托管标识代表你访问存储帐户。

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>使用媒体服务帐户的托管标识访问 Key Vault 以访问客户密钥

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
