---
title: 托管标识
description: 媒体服务可与 Azure 托管标识结合使用。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 02f832c7fc25003950d6a112d951c07d1332e08a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454361"
---
# <a name="managed-identities"></a>托管标识

开发人员面临的一个共同挑战是如何管理密码和凭据，以确保不同服务之间的通信安全。 在 Azure 上，托管标识可为 Azure AD 中的 Azure 资源提供标识并使用它来获取 Azure Active Directory (Azure AD) 令牌，从而使开发人员无需管理凭据。

## <a name="media-services-managed-identity-scenarios"></a>媒体服务托管标识方案

目前有以下三种方案可将托管标识与媒体服务配合使用：

- [向媒体服务帐户授予对 Key Vault 的访问权限以启用客户管理的密钥](security-encrypt-data-managed-identity-cli-tutorial.md)
- [授予媒体服务帐户对存储帐户的访问权限，以允许媒体服务绕过 Azure 存储网络 ACL](security-access-storage-managed-identity-cli-tutorial.md)
- 允许其他服务（例如 VM 或 [Azure Functions](security-function-app-managed-identity-cli-tutorial.md)）访问媒体服务

在前两个方案中，托管标识用于向媒体服务帐户授予对其他服务的访问权限。  在第三个方案中，服务具有用于访问媒体服务的托管标识。

:::image type="content" source="media/diagrams/managed-identities-scenario-comparison.svg" alt-text="托管标识方案比较":::

> [!NOTE]
> 这些方案可以合并。 你可以为媒体服务帐户创建托管标识（例如，为了访问客户管理的密钥而这样做），还可以为 Azure Functions 资源创建托管标识，以便访问媒体服务帐户。

## <a name="tutorials-and-how-tos"></a>教程和操作说明

请尝试这些教程，获取一些将托管标识与媒体服务配合使用的实践体验。

- [CLI：使用 Key Vault 中的密钥将数据加密到媒体服务帐户中](security-encrypt-data-managed-identity-cli-tutorial.md)
- [CLI：允许媒体服务访问配置为阻止来自未知 IP 地址的请求的存储帐户](security-access-storage-managed-identity-cli-tutorial.md)
- [CLI：授予函数应用访问媒体服务帐户的权限](security-function-app-managed-identity-cli-tutorial.md)
- [门户：使用 Azure 门户将客户管理的密钥或 BYOK 与媒体服务配合使用](security-customer-managed-keys-portal-tutorial.md)
- [POSTMAN/REST：将客户管理的密钥或 BYOK 与媒体服务 REST API 配合使用](security-customer-managed-keys-rest-postman-tutorial.md)

## <a name="further-reading"></a>延伸阅读

若要详细了解托管标识可以为你和你的 Azure 应用程序执行哪些操作，请参阅 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

若要详细了解 Azure Functions，请参阅[关于 Azure Functions](../../azure-functions/functions-overview.md)
