---
title: Azure 委派资源管理
description: Azure 委托资源管理是 Azure Lighthouse 的关键部分，它使服务提供商可以灵活精确地管理大规模委托的资源。
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d484e61fc4ab3714eb362b26d64d449890065888
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92203851"
---
# <a name="azure-delegated-resource-management"></a>Azure 委派资源管理

Azure 委托资源管理是 [Azure Lighthouse](../overview.md) 的关键组成部分之一。 借助 Azure 委托资源管理，服务提供商可以简化客户参与和载入体验，同时灵活精确地管理大规模委托资源。 客户可以控制哪些服务提供商可以访问其租户，还可以控制谁可以访问其租户、这些人可以访问哪些资源以及可以执行哪些操作。

## <a name="what-is-azure-delegated-resource-management"></a>什么是 Azure 委派资源管理？

通过 Azure 委派资源管理，可以将资源从一个租户逻辑投影到另一个租户。 因此某个 Azure Active Directory (Azure AD) 租户中的授权用户可以跨属于其客户的不同 Azure AD 租户执行管理操作。 服务提供商可以登录到自己的 Azure AD 租户，并且有权在委派的客户订阅和资源组中工作。 这样，他们就可以代表其客户执行管理操作，而无需登录每个单独的客户租户。

> [!TIP]
> 还可在[具有多个其自己的 Azure AD 租户的企业中](enterprise.md)使用 Azure 委派资源管理，以简化跨租户管理。

借助 Azure 委派资源管理，授权用户可直接处理客户订阅的上下文，而无需具有客户的租户帐户或成为客户的租户的共同所有者。

[跨租户管理体验](cross-tenant-management-experience.md)可让你使用 Azure Policy、Azure 安全中心等 Azure 管理服务更有效率地工作。 所有服务提供商的活动都在活动日志中进行跟踪，该日志存储在客户的租户中（并且可供管理租户中的用户查看）。 这意味着管理租户和托管租户中的用户都可以轻松识别与任何更改关联的用户。

你可以[将新的托管服务产品/服务类型发布到 Azure 市场](../how-to/publish-managed-services-offers.md)，以便轻松地将客户加入 Azure Lighthouse。 或者，可以[通过部署 Azure 资源管理器模板来完成载入过程](../how-to/onboard-customer.md)。

## <a name="how-azure-delegated-resource-management-works"></a>Azure 委派资源管理的工作原理

概括而言，Azure 委派资源管理的工作原理如下：

1. 首先，你需要确定你的组、服务主体或用户在管理客户的 Azure 资源时所需的访问权限（角色）。 访问定义包含管理租户 ID 以及从你的租户映射到[内置 roleDefinition 值](../../role-based-access-control/built-in-roles.md)（“Contributor”、“VM Contributor”、“Reader”，等等）的 principalId 标识。
2. 可通过以下两种方式之一指定此访问权限并将客户加入 Azure Lighthouse：
   - [发布 Azure 市场托管服务产品/服务](../how-to/publish-managed-services-offers.md)（专用或公用），客户会接受这些产品/服务
   - 为一个或多个特定订阅或资源组[将 Azure 资源管理器模板部署到客户的租户](../how-to/onboard-customer.md)

3. 客户加入后，授权用户可以登录你的管理租户，根据你定义的访问权限在给定的客户范围内执行任务。 客户可以查看服务提供商的操作，并可以选择根据需要来删除访问权限。

> [!NOTE]
> 你可以管理位于不同[区域](../../availability-zones/az-overview.md#regions)的委托资源。 但是，不支持跨[国家云](../../active-directory/develop/authentication-national-cloud.md)和 Azure 公有云或跨两个不同的国家云进行订阅委托。

## <a name="support-for-azure-delegated-resource-management"></a>Azure 委派资源管理支持

如果需要关于 Azure 委派资源管理方面的帮助，可以在 Azure 门户中打开支持请求。 对于“问题类型”，请选择“技术”。 选择订阅，然后选择“Lighthouse”（在“监视与管理”下） 。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解 [Azure 市场中的托管服务产品](managed-services-offers.md)。
