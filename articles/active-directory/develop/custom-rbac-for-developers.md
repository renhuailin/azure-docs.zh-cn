---
title: 适用于应用程序开发人员的自定义基于角色的访问控制 (RBAC) - Microsoft 标识平台
description: 了解什么是自定义 RBAC，以及为什么在应用程序中实现自定义 RBAC 很重要。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/28/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: john.garland, maggie.marxen, ian.bennett, marsma
ms.openlocfilehash: f1378d1e011deaddf793dea9bebc7b099bedde9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737308"
---
# <a name="role-based-access-control-for-application-developers"></a>适用于应用程序开发人员的基于角色的访问控制

基于角色的访问控制 (RBAC) 使某些用户或组拥有针对他们有权访问的资源、他们可以对这些资源执行的操作以及资源管理人员的特定权限。 本文介绍特定于应用程序的基于角色的访问控制。

> [!NOTE]
> 应用程序基于角色的访问控制不同于 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)和 [Azure AD 基于角色的访问控制](../roles/custom-overview.md#understand-azure-ad-role-based-access-control)。 Azure 自定义角色和内置角色都是 Azure RBAC 的一部分，可帮助你管理 Azure 资源。 Azure AD RBAC 使你能够管理 Azure AD 资源。



## <a name="what-are-roles"></a>什么是角色？

基于角色的访问控制 (RBAC) 是一种常用的机制，用于在应用程序中强制进行授权。 使用 RBAC 时，应用程序开发人员会定义角色，而不是对各个用户或组进行授权。 然后，管理员再将角色分配给不同的用户和组，从而控制谁有权访问哪些内容和功能。

RBAC 可帮助应用开发人员管理资源以及用户可以对这些资源执行哪些操作。 RBAC 还使应用开发人员能够控制应用用户有权访问的应用区域。 虽然管理员可以使用“需要进行用户分配”属性控制哪些用户有权访问应用，但开发人员需要考虑到应用中的特定用户以及用户可以在应用中执行哪些操作。

作为应用开发人员，需要首先在 Azure AD 管理中心内应用的注册部分创建角色定义。 角色定义包括为分配到该角色的用户返回的值。 然后，开发人员可以使用此值实现应用程序逻辑，以确定这些用户在应用程序中能够或不能执行哪些操作。

## <a name="options-for-adding-rbac-to-apps"></a>用于向应用添加 RBAC 的选项

当应用程序中包括基于角色的访问控制授权时，必须考虑一些注意事项。 其中包括:
- 定义应用程序的授权需求所需的角色。 
- 为经过身份验证的用户应用、存储和检索相关角色。 
- 根据分配给当前用户的角色影响所需的应用程序行为。 

定义角色后，Microsoft 标识平台支持多个不同的解决方案，可以使用这些解决方案为经过身份验证的用户应用、存储和检索角色信息。 这些解决方案包括应用角色、Azure AD 组，以及使用自定义数据存储获取用户角色信息。

开发人员可以灵活地提供他们自己的实现，用于指定如何将角色分配解释为应用程序权限。 这可能涉及到利用中间件或他们应用程序的平台或相关库提供的其他功能。 应用通常会将用户角色信息作为声明接收，并且将基于这些声明决定用户权限。

### <a name="app-roles"></a>应用角色

Azure AD 支持声明应用角色以用于应用程序注册。 当用户登录应用程序时，Azure AD 将为该应用程序提供该用户被授予的每一角色的[角色声明](./access-tokens.md#payload-claims)。 然后，接收包含这些声明的令牌的应用程序可以使用此信息决定用户在被分配的角色基础上可以执行哪些权限。

### <a name="groups"></a>组

开发人员还可使用 [Azure AD 组](../fundamentals/active-directory-manage-groups.md)在应用程序中实现 RBAC，其中特定组中用户的成员身份被解释为他们的角色成员身份。 使用 Azure AD 组时，Azure AD 将包括[组声明](./access-tokens.md#payload-claims)，该声明将包含当前 Azure AD 租户中用户被分配到的所有组的标识符。 然后，接收包含这些声明的令牌的应用程序可以使用此信息决定用户在被分配的角色基础上可以执行哪些权限。

> [!IMPORTANT]
> 使用组时，开发人员需要了解[超额声明](./access-tokens.md#payload-claims)的概念。 默认情况下，如果用户所属的组数超过超额限制（SAML 令牌的为 150 个，JWT 令牌的为 200 个，使用隐式流颁发的令牌的仅为 6 个），Azure AD 将不会在令牌中发出组声明。 相反，它将在令牌中包括一个“超额声明”，指示令牌的使用者需要查询图形 API 以检索用户的组成员身份。 有关使用超额声明的详细信息，请参阅[访问令牌中的声明](./access-tokens.md#claims-in-access-tokens)。 可以仅发出分配给应用程序的组，但[基于组的分配](../manage-apps/assign-user-or-group-access-portal.md)需要 Azure Active Directory Premium P1 或 P2 版本。

### <a name="custom-data-store"></a>自定义数据存储

应用角色和组都将有关用户分配的信息存储在 Azure AD 目录中。 开发人员可用于管理用户角色信息的另一个选项是在自定义数据存储中维护目录外部的信息。 例如，在 SQL 数据库、Azure 表存储或 Azure Cosmos DB 表 API 中。

通过使用自定义存储，开发人员可以额外自定义和控制如何向用户分配角色以及如何表示这些角色。 但是，额外的灵活性也带来了更多责任。 例如，当前没有任何机制可将此信息包括在从 Azure AD 返回的令牌中。 如果开发人员要在自定义数据存储中维护角色信息，则需要让应用检索角色。 这通常使用中间件中定义的扩展点完成，这些扩展点可在被用于开发应用程序的平台中使用。 此外，开发人员负责妥善保护自定义数据存储。

> [!NOTE]
> 通过使用 [Azure AD B2C 自定义策略](../../active-directory-b2c/custom-policy-overview.md)，可以与自定义数据存储进行交互，并在令牌中包括自定义声明。

## <a name="choosing-an-approach"></a>选择方法

通常，应用角色是建议的解决方案。 应用角色提供最简单的编程模型，用于 RBAC 实现。 但是，特定的应用程序要求可能提示有其他更好的解决方案。

开发人员可以使用应用角色来控制用户是否可以登录到应用，或者应用是否可以获取 Web API 的访问令牌。 当开发人员想要自己描述和控制他们应用中的授权参数时，比起 Azure AD 组，首选使用的是应用角色。 例如，使用组进行授权的应用将在下一个租户中中断，因为组 ID 和名称可能不同。 使用应用角色的应用处于安全状态。 事实上，出于同样的原因，将组分配给应用角色在 SaaS 应用中非常流行。

尽管应用角色或组可用于授权，但两者之间的主要差异可能会影响哪个解决方案是给定场景下最佳的解决方案。

|          |应用角色 |Azure AD 组 |自定义数据存储|
|----------|-----------|------------|-----------------|
|**编程模型** |**最简单**。 它们特定于应用程序，并在应用注册中定义。 它们随应用程序一起移动。|**更复杂**。 租户之间的组 ID 各不相同，可能需要考虑超额声明。 组不特定于应用，而是特定于 Azure AD 租户。|**最复杂**。 开发人员必须实现存储和检索角色信息的方式。|
|**角色值在 Azure AD 租户之间是静态的**|是  |否 |具体取决于实现。|
|**角色值可在多个应用程序中使用**|不是。 除非在每个应用注册中重复角色配置。|是 |是 |
|**信息存储在目录中**|是  |是 |否 |
|**信息通过令牌传递**|是（角色声明）  |是*（组声明） |不是。 通过自定义代码在运行时检索。 |
|**生存期**|在目录中的应用注册中。 删除应用注册时被删除。|在目录中。 删除应用注册时完整保留。 |在自定义数据存储中。 与应用注册无关。|


> [!NOTE]
> 是* - 在超额的情况下，可能需要在运行时检索组声明。

## <a name="next-steps"></a>后续步骤

- [如何将应用角色添加到应用程序并在令牌中接收它们](./howto-add-app-roles-in-azure-ad-apps.md)。
- [将应用程序注册到 Microsoft 标识平台](./quickstart-register-app.md)。
- [Azure 标识管理和访问控制安全最佳做法](../../security/fundamentals/identity-management-best-practices.md)。