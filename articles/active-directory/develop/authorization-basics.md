---
title: 授权基础知识 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台中有关授权的基础知识。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: cc636c60ee88d69e263ae5a2d9d599e2230184cd
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122206099"
---
# <a name="authorization-basics"></a>授权基础知识

授权（有时简写为 AuthZ）用于设置权限，这些权限用于评估对资源或功能的访问权限。  相比而言，身份验证（有时简写为 AuthN）侧重于证明某实体（如用户或服务）确实是其所称的身份。

授权可包括指定实体能够访问的功能（或资源），或者实体能够访问的数据及其可对该数据执行的操作。 这通常称为“访问控制”。

> [!NOTE]
> 身份验证和授权这两个概念不局限于用户。 通常，构建服务或守护程序应用程序的目的是让其以自身身份而不是代表特定用户发出资源请求。 讨论这些主题时，术语“实体”用于指代用户或应用程序。


## <a name="authorization-approaches"></a>授权方法

可通过多种常见方法来处理授权。 [基于角色的访问控制](./custom-rbac-for-developers.md)目前是使用 Microsoft 标识平台的最常见方法。


### <a name="authentication-as-authorization"></a>身份验证即授权 

最简单的授权形式可能是根据是否已对发出请求的实体进行身份验证来授予或拒绝访问权限。 如果请求者可证明自己是所自称的身份，则可访问受保护的资源或功能。

### <a name="access-control-lists"></a>访问控制列表

通过访问控制列表 (ACL) 进行的授权涉及到维护明确的特定实体列表，这些实体有权或无权访问资源或功能。 ACL 提供对身份验证即授权的精细控制，但管理工作会随着实体数量的增加而变得困难。

### <a name="role-based-access-control"></a>基于角色的访问控制 

基于角色的访问控制 (RBAC) 可能是在应用程序中强制授权的最常见方法。 使用 RBAC 时，会对角色进行定义，以说明实体可执行的活动类型。 应用程序开发人员向角色而非单个实体授予访问权限。 然后，管理员可再将角色分配给不同的实体，从而控制哪些实体有权访问哪些资源和功能。

在高级 RBAC 实现中，可将角色映射到权限集合，其中权限描述了可执行的细化操作或活动。 然后，会将角色配置为权限组合。 通过将授予给为实体分配的各种角色的权限进行相交，计算应用程序中实体的总体权限集。 此方法的一个很好的示例是用于管理对 Azure 订阅中资源的访问权限的 RBAC 实现。

> [!NOTE]
> [应用程序 RBAC](./custom-rbac-for-developers.md) 不同于 [Azure RBAC](/azure/role-based-access-control/overview) 和 [Azure AD RBAC](../roles/custom-overview.md#understand-azure-ad-role-based-access-control)。 Azure 自定义角色和内置角色都是 Azure RBAC 的一部分，可帮助你管理 Azure 资源。 Azure AD RBAC 使你能够管理 Azure AD 资源。

### <a name="attribute-based-access-control"></a>基于属性的访问控制 

基于属性的访问控制 (ABAC) 是一种更精细的访问控制机制。 在此方法中，规则会应用于实体的属性、所访问的资源和当前环境，以确定是否允许访问某些资源或功能。 例如，可能只允许拥有管理员身份的用户在工作日上午 9 点至下午 5 点期间访问使用元数据标记“工作时间仅限管理员”标识的文件。 在这种情况下，通过检查用户的属性（状态为管理员）、资源属性（文件上的元数据标记）以及环境属性（当前时间）来确定其是否具有访问权限。

ABAC 的一项优势是，可通过规则和条件评估实现更精细的动态访问控制，而无需创建大量特定的角色和 RBAC 分配。

使用 Azure Active Directory 实现 ABAC 的一种方法是使用[动态组](../enterprise-users/groups-create-rule.md)。 动态组可让管理员基于具有所需值的特定用户属性将用户动态分配到组。  例如，可以创建一个作者组，其中包含职务“作者”的所有用户都被动态分配到作者组。  可将动态组与 RBAC 结合用于授予，在此情况下，将角色映射到组，并将用户动态分配到组。

## <a name="implementing-authorization"></a>实现授权

授权逻辑通常是在需要访问控制的应用程序或解决方案中实现的。 在许多情况下，应用程序开发平台提供中间件或其他 API 解决方案，以简化授权的实现。 例如，在 ASP.NET 中使用[AuthorizeAttribute](/aspnet/core/security/authorization/simple?view=aspnetcore-5.0&preserve-view=true) 或在 Angular 中使用[路由守卫](./scenario-spa-sign-in.md?tabs=angular2#sign-in-with-a-pop-up-window)。

如果授权方法依赖于经过身份验证的实体的相关信息，应用程序将评估在身份验证过程中交换的信息。 例如，使用[安全令牌](./security-tokens.md)中提供的信息。 对于安全令牌中未包含的信息，应用程序可能会对外部资源进行额外调用。

开发人员不必将授权逻辑完全嵌入其应用程序中。 而是，专用授权服务可用于集中实现和管理授权。


## <a name="next-steps"></a>后续步骤

- 若要了解应用程序中自定义的基于角色的访问控制实现，请参阅[面向应用程序开发人员的基于角色的访问控制](./custom-rbac-for-developers.md)。
- 若要了解注册应用程序以便它可以与 Microsoft 标识平台集成的过程，请参阅[应用程序模型](./application-model.md)。
- 有关配置基于身份验证的简单授权的示例，请参阅[将应用服务或 Azure Functions 应用配置为使用 Azure AD 登录](/azure/app-service/configure-authentication-provider-aad)。
