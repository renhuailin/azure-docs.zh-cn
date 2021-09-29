---
title: 将 Azure 计划下的订阅从一个合作伙伴转移到另一合作伙伴（预览）
description: 本文帮助你了解在转移 Azure 订阅的计费所有权前后需要了解的内容。
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 6ce3a4b19bd1ba32a466bede87e111744e1a8a52
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658462"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>将 Azure 计划下的订阅从一个合作伙伴转移到另一合作伙伴（预览）

本文帮助你了解在转移 Azure 订阅的计费所有权前后需要了解的内容。 若要开始将 Azure 订阅从一个 Microsoft 合作伙伴的 Azure 计划下到转移另一个的，你需要联系你的合作伙伴。 合作伙伴将向你发送有关如何开始的说明。 转移过程完成后，订阅的计费所有权将更改。

## <a name="user-access"></a>用户访问权限

在转移期间，使用 Azure 基于角色的访问控制 (Azure RBAC) 为现有用户、组或服务主体分配的访问权限不受影响。 [Azure RBAC](../../role-based-access-control/overview.md) 有助于管理谁有权访问 Azure 资源、这些人可以对这些资源执行哪些操作，以及他们有权访问哪些区域。 通过订阅转移，新合作伙伴不会获得对你的资源的任何 Azure RBAC 访问权限。 旧合作伙伴将保留其 Azure RBAC 访问权限。

因此，请务必删除旧合作伙伴的 Azure RBAC 访问权限，并为新合作伙伴授予访问权限。 有关为新合作伙伴提供访问权限的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md) 要详细了解如何删除旧合作伙伴的 Azure RBAC 访问权限，请参阅[删除 Azure 角色分配](../../role-based-access-control/role-assignments-remove.md)。

此外，新合作伙伴不会自动获取对你的订阅的[代表客户管理 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 访问权限。 合作伙伴需要 AOBO 才能代表你管理 Azure 订阅。 有关 Azure 特权的详细信息，请参阅[获取管理客户服务或订阅的权限](/partner-center/customers-revoke-admin-privileges)。

## <a name="stop-a-transfer"></a>停止转移

收到关于已提交转移请求的确认函后，如果你不希望转移继续进行，可以使用以下选项之一。

- 如果当前的合作伙伴尚未接受该请求，你可以要求你的新合作伙伴取消他们启动的转移请求（当状态为挂起时）。
- 要求当前合作伙伴在收到转移请求时不执行任何操作。 未经当前合作伙伴的同意，无法继续转移。 请求将过期。
- 可以删除与新合作伙伴的经销商关系。 此操作将使订阅不可移动。 它有效地取消请求。

你还可以使用 [Microsoft 法律](https://www.microsoft.com/legal/)网站上的任何选项寻求帮助、举报不当行为或可疑活动。 报告问题的选项位于“合规性与道德”下方。

## <a name="next-steps"></a>后续步骤

- 若要为新合作伙伴提供 Azure RBAC 访问权限，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [获取管理客户服务或订阅的权限](/partner-center/customers-revoke-admin-privileges)。