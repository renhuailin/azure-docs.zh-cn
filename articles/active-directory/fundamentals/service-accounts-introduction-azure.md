---
title: 保护 Azure Active Directory 服务帐户简介
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
ms.openlocfilehash: d657f1df14b083631227cb7c19f64b65be8801d0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010459"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>保护 Azure 服务帐户简介

Azure Active Directory 本机有三种类型的服务帐户：托管标识、服务主体和基于用户的服务帐户。 服务帐户是一种特殊类型的帐户，用于表示非人类实体，如应用程序、API 或其他服务。 这些实体在服务帐户提供的安全上下文中运行。 

## <a name="types-of-azure-active-directory-service-accounts"></a>Azure Active Directory 服务帐户类型

对于在 Azure 中托管的服务，如果可能，建议使用托管标识，否则使用服务主体。 托管标识不能用于在 Azure 外部托管的服务。 在这种情况下，我们建议使用服务主体。 如果可以使用托管标识或服务主体，请使用。 建议不要将 Azure Active Directory 用户帐户用作服务帐户。 有关摘要的信息，请参阅下表。
 

| 服务托管| 托管标识| 服务主体| Azure 用户帐户 |
| - | - | - | - |
|服务托管在 Azure 中。| 是的。 <br>建议（如果服务 <br>支持托管标识）。| 是的。| 建议不要使用。 |
| 服务未托管在 Azure 中。| 否| 是。 推荐。| 建议不要使用。 |
| 服务为多租户| 否| 是。 推荐。| 不是。 |


## <a name="managed-identities"></a>托管标识

托管标识是安全的 Azure Active Directory (Azure AD) 标识，创建的目的是为 Azure 资源提供标识。 [托管标识分为两种类型](../managed-identities-azure-resources/overview.md#managed-identity-types)： 
 
* 系统分配的托管标识可以直接分配给服务实例。 

* 用户分配的托管标识可以作为独立的资源创建。 

有关详细信息，请参阅[保护托管标识](service-accounts-managed-identities.md)。 有关托管标识的一般信息，请参阅[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md)

## <a name="service-principals"></a>服务主体

如果你无法使用托管标识来表示你的应用程序，请使用服务主体。 服务主体可与单租户和多租户应用程序一起使用。 

服务主体是单个 Azure AD 租户中应用程序对象的本地表示形式。 它充当应用程序实例的标识，定义可访问应用程序的人员，以及应用程序可访问的资源。 服务主体是在使用应用程序的每个租户中（本地）创建的，会引用全局唯一的应用程序对象。 租户负责保护服务主体的登录和对资源的访问。

使用服务主体的身份验证有两种机制：客户端证书和客户端机密。 证书更安全：如果可能，请使用客户端证书。 与客户端机密不同，客户端证书不能被意外嵌入代码中。

有关保护服务主体的信息，请参阅[保护服务主体](service-accounts-principal.md)。

 
## <a name="next-steps"></a>后续步骤


有关保护 Azure 服务帐户的详细信息，请参阅：

[保护托管标识](service-accounts-managed-identities.md)

[保护服务主体](service-accounts-principal.md)

[管控 Azure 服务帐户](service-accounts-governing-azure.md)
