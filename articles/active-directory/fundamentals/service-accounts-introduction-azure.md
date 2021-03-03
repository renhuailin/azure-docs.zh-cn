---
title: 保护 Azure Active Directory 服务帐户的简介
description: 说明 Azure Active Directory 中可用的服务帐户类型。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 049025a5d871f1dd26e5dab498756aa44d2ebfe2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693017"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>保护 Azure 服务帐户的简介

有三种类型的服务帐户适用于 Azure Active Directory：托管标识、服务主体和基于用户的服务帐户。 服务帐户是一种特殊类型的帐户，用于表示非人力资源实体，如应用程序、API 或其他服务。 这些实体在服务帐户提供的安全上下文中运行。 

## <a name="types-of-azure-active-directory-service-accounts"></a>Azure Active Directory 服务帐户的类型

对于在 Azure 中托管的服务，建议使用托管标识（如果可能）和服务主体（如果没有）。 托管标识不能用于在 Azure 外部托管的服务。 在这种情况下，我们建议使用服务主体。 如果可以使用托管标识或服务主体，请执行此操作。 建议你不要将 Azure Active Directory 的用户帐户用作服务主体。 有关摘要，请参阅下表。
 

| 服务托管| 托管标识| 服务主体| Azure 用户帐户 |
| - | - | - | - |
|服务托管在 Azure 中。| 是的。 <br>如果服务 <br>支持托管标识。| 是的。| 不建议。 |
| 服务未托管在 Azure 中。| 否| 可以。 推荐。| 不建议。 |
| 服务是多租户服务| 否| 可以。 推荐。| 否。 |


## <a name="managed-identities"></a>托管标识

托管标识是 Azure Active Directory (Azure AD 创建的) 标识，以提供 Azure 资源的标识。 有 [两种类型的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types)： 
 
* 系统分配的托管标识可以直接分配给服务的实例。 

* 用户分配的托管标识可以创建为独立资源。 

有关详细信息，请参阅 [保护托管标识](service-accounts-managed-identities.md)。 有关托管标识的常规信息，请参阅 [Azure 资源的托管标识是什么？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="service-principals"></a>服务主体

如果你无法使用托管标识来表示你的应用程序，请使用服务主体。 服务主体可与单租户和多租户应用程序一起使用。 

服务主体是单个 Azure AD 租户中的应用程序对象的本地表示形式。 它充当应用程序实例的标识，定义可访问应用程序的人员，以及应用程序可访问的资源。 在 (local 中创建服务主体，以) 使用应用程序的每个租户，并引用全局唯一的应用程序对象。 租户保护服务主体的登录和资源访问权限。

使用服务主体进行身份验证的机制有两种：客户端证书和客户端机密。 证书更安全：如有可能，请使用客户端证书。 与客户端密码不同的是，客户端证书不能在代码中意外嵌入。

有关保护服务主体的信息，请参阅保护服务主体。

 
## <a name="next-steps"></a>后续步骤


有关保护 Azure 服务帐户的详细信息，请参阅：

[保护托管标识](service-accounts-managed-identities.md)

[保护服务主体](service-accounts-principal.md)

[管理 Azure 服务帐户](service-accounts-governing-azure.md)



