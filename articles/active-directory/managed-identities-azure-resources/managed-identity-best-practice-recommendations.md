---
title: 托管系统标识的最佳做法建议
description: 有关何时使用用户分配托管标识与系统分配托管标识的建议
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/21/2021
ms.author: barclayn
ms.openlocfilehash: dec6cb642c5a5899354912f133decde45d631406
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953329"
---
# <a name="managed-identity-best-practice-recommendations"></a>托管标识的最佳做法建议

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="choosing-system-or-user-assigned-managed-identities"></a>选择系统分配还是用户分配的托管标识

与系统分配的托管标识相比，用户分配的托管标识适用的方案范围更加广泛。 参阅下表，了解适用于用户分配或系统分配的一些方案和建议。

用户分配的标识可供多个资源使用，但其生命周期与和其关联的资源的生命周期保持分离。 [查阅支持托管标识的资源](./services-support-managed-identities.md)。

在此生命周期，你可以分离资源创建和标识管理职责。 可以预先配置用户分配的标识及其角色分配，然后再配置需要这些项目的资源。 创建资源的用户只需要访问权限即可分配用户分配的标识，无需创建新的标识或角色分配。  

由于创建和删除系统分配标识的同时也会创建和删除相关资源，因此无法提前创建角色分配。 如果创建资源的用户也没有创建角色分配的权限，则此序列可能会导致基础结构部署失败。

如果你的基础结构要求多个资源需可访问相同资源，则可以为这些资源分配用户分配的标识。 这样可降低管理开销，因为要管理的不同标识和角色分配较少。

若你要求每个资源都有其自己的标识或其中包含需要唯一权限集的资源，并且你希望在删除资源时也删除相应标识，则应当使用系统分配的标识。


| 方案| 建议|说明|
|---|---|---|
| 使用托管标识快速创建资源（如临时计算） |  用户分配的标识 | 若尝试在短时间内创建多个托管标识（例如，部署多个虚拟机，其中每个虚拟机都有自己的系统分配标识），则可能会超出创建 Azure Active Directory 对象时的速率限制，请求将失败并出现 HTTP 429 错误。 <br/><br/>若使用系统分配的标识快速创建或删除资源，则可能还会超出 Azure Active Directory 中的资源数限制。 当任何资源都无法再访问已删除的系统分配标识时，该标识将计入限制，并在 30 天后完全清除。<br/><br/>部署与单一用户分配标识关联的资源时，只需在 Azure Active Directory 中创建一个服务主体，以防超出速率限制。 使用预先创建的单个标识还可降低复制延迟（使用其各自的标识创建多个资源时，可能会出现复制延迟）带来的风险。<br/><br/>阅读有关 [Azure 订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)的更多信息。 |
| 复制资源/应用程序 | 用户分配的标识  |  执行相同任务的资源（例如，在虚拟机的应用服务和应用程序中运行的复制 Web 服务器或相同功能）通常需要相同的权限。 <br/><br/>通过使用相同的用户分配标识，所需创建的角色分配将会变少，管理开销也会相应降低。 资源类型可以不同。 
|合规性| 用户分配的标识  | 如果你的组织要求所有标识创建都必须经过审批，则在多个资源中使用用户分配的单个标识所需通过的审批将少于系统分配的标识（这些标识将与新资源一同创建）。 |
需先进行访问，才可部署资源 |用户分配的标识| 有些资源可能需要在部署过程中访问特定 Azure 资源。<br/><br/>在这种情况下，系统分配的标识可能不会按时创建，因此应当使用现存的用户分配标识。|
审核日志|系统分配的标识|如果需要记录执行某项操作的特定资源（而非标识），请使用系统分配的标识。|
权限生命周期管理|系统分配的标识|如果需要将资源的权限连同该资源一起删除，请使用系统分配的标识。

### <a name="using-user-assigned-identities-to-reduce-administration"></a>使用用户分配的标识来减少管理工作

下方关系图将演示分别使用系统分配标识和用户分配标识来允许多个虚拟机访问两个存储帐户时出现的差异。 

此图显示使用系统分配标识的四个虚拟机。 每个虚拟机获分配的角色完全相同，以使其可以访问两个存储帐户。

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities.png" alt-text="四个虚拟机正在使用系统分配的标识访问存储帐户和密钥保管库。":::

若将用户分配的标识与四个虚拟机关联，则只需进行 2 次角色分配，而使用系统分配标识将需进行 8 次角色分配。 如果虚拟机的标识需要更多角色分配，则这些角色将获得与此标识相关联的所有资源的访问权限。

:::image type="content" source="media/managed-identity-best-practice-recommendations/user-assigned-identities.png" alt-text="四个虚拟机正在使用用户分配标识访问存储帐户和密钥保管库。":::

你还可以使用安全组来减少所需的角色分配次数。 此图显示使用系统分配标识的四个虚拟机，其中这些标识已添加到安全组，而角色分配也已添加到该组而不是系统分配标识。 虽然结果很类似，但此配置不提供与用户分配标识相同的资源管理器模板功能。

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities-in-a-group.png" alt-text="向包含角色分配的安全组添加了系统分配标识的四个虚拟机。":::

### <a name="multiple-managed-identities"></a>多个托管标识

支持托管标识的资源可以同时使用一个系统分配标识和一个或多个用户分配标识。 

此模型可让你灵活使用共享的用户分配标识，并在需要时应用精细权限。

在下方示例中，“虚拟机 3”和“虚拟机 4”可以访问存储帐户和密钥保管库，具体取决于在进行身份验证时使用的用户分配标识。

:::image type="content" source="media/managed-identity-best-practice-recommendations/multiple-user-assigned-identities.png" alt-text="四个虚拟机，其中两个虚拟机使用多个用户分配标识。":::

在下方示例中，“虚拟机 4”还使用可允许其访问存储帐户和密钥保管库的用户分配标识，具体取决于在进行身份验证时使用的标识。 系统分配标识的角色分配应根据相应虚拟机的情况进行。

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-and-user-assigned-identities.png" alt-text="四个虚拟机，其中一个同时使用系统分配标识和用户分配标识。":::

## <a name="limits"></a>限制 

查看[托管标识](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits)以及[自定义角色和角色分配](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits)的限制。

## <a name="maintenance"></a>维护

系统分配标识会在删除资源时自动删除，而用户分配标识的生命周期独立于与之关联的任何资源。

如果不再需要用户分配标识，则需要手动将其删除（即使没有任何资源与之关联）。

删除系统分配或用户分配的托管标识后，相关角色分配不会自动删除。 因此，应当手动删除这些角色分配，以免超出每个订阅的角色分配数上限。 

在门户中查看时，与已删除的托管标识关联的角色分配将会显示“找不到标识”。 [了解详细信息](../../role-based-access-control/troubleshooting.md#role-assignments-with-identity-not-found)。

:::image type="content" source="media/managed-identity-best-practice-recommendations/identity-not-found.png" alt-text="找不到角色分配的标识。":::