---
title: Azure RBAC 最佳做法
description: 使用 Azure 基于角色的访问控制 (Azure RBAC) 的最佳做法。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: d58398c42cdc6faed758e5dba3431e0841fc0b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555568"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC 最佳做法

本文介绍了使用 Azure 基于角色的访问控制 (Azure RBAC) 的一些最佳做法。 这些最佳做法源自我们的 Azure RBAC 经验和客户经验。

## <a name="only-grant-the-access-users-need"></a>仅授予用户所需的访问权限

使用 Azure RBAC，可以在团队中实现职责分离，仅向用户授予他们执行作业所需的访问权限。 请勿向每个人授予 Azure 订阅或资源的无限制权限，只能允许他们在特定的范围执行某些操作。

规划访问控制策略时，最佳做法是授予用户完成工作所需的最低权限。 即使最初看起来更方便操作，也应避免在更广泛的范围内分配更广泛的角色。 创建自定义角色时，只包括用户需要的权限。 通过限制角色和范围，可以对在安全主体受到入侵的情况下会面临风险的具体资源进行限制。

下图显示了与 Azure RBAC 使用有关的建议模式。

![Azure RBAC 和最低权限](./media/best-practices/rbac-least-privilege.png)

有关如何分配角色的信息，请参阅[使用 Azure 门户分配 Azure 角色](role-assignments-portal.md)。

## <a name="limit-the-number-of-subscription-owners"></a>限制订阅所有者的数量

最多只能有 3 个订阅所有者，这样可降低被入侵的所有者做出违规行为的可能性。 可以在 Azure 安全中心内监视此建议措施。 有关安全中心内的其他标识和访问建议，请参阅[安全建议 - 参考指南](../security-center/recommendations-reference.md)。

## <a name="use-azure-ad-privileged-identity-management"></a>使用 Azure AD Privileged Identity Management

为了保护特权帐户免受恶意网络攻击，可以使用 Azure Active Directory Privileged Identity Management (PIM) 来降低权限的暴露时间，并通过报表和警报增加对使用的可见性。 PIM 提供对 Azure AD 和 Azure 资源的实时特权访问权限，有助于保护特权帐户。 访问可能有时间限制，在超过时限后会自动撤销特权。 

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](../active-directory/privileged-identity-management/pim-configure.md)。

## <a name="assign-roles-to-groups-not-users"></a>将角色分配给组，而不是用户

为了使角色分配更易于管理，请避免将角色直接分配给用户。 而是将角色分配给组。 将角色分配给组而不是用户还有助于最大程度地减少角色分配的数量，[每个订阅的角色分配数限制为 2,000](troubleshooting.md#azure-role-assignments-limit)。 

## <a name="next-steps"></a>后续步骤

- [排查 Azure RBAC 问题](troubleshooting.md)
