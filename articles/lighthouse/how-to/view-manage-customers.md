---
title: 查看和管理 Azure 门户中的客户及委派资源
description: 作为使用 Azure Lighthouse 的服务提供商或企业，你可以通过转到 Azure 门户中的“我的客户”，查看所有委派的资源和订阅。
ms.date: 08/12/2021
ms.topic: how-to
ms.openlocfilehash: a29f34983bc42d74efd65a45605bb99944897345
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861007"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>查看和管理 Azure 门户中的客户及委派资源

使用 [Azure Lighthouse](../overview.md) 的服务提供商可以使用 [Azure 门户](https://portal.azure.com)中“我的客户”页来查看委派的客户资源和订阅。

> [!TIP]
> 在此处我们将提到服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)可以使用相同的过程来整合其管理体验。

若要访问 Azure 门户中“我的客户”页，请选择“所有服务”，然后搜索“我的客户”并选择它。 还可以通过在 Azure 门户顶部附近的搜索框中输入“我的客户”来找到它。

请记住，“我的客户”页顶部的“客户”部分仅显示有关已通过 Azure Lighthouse 将订阅或资源组委托给你的 Azure Active Directory (Azure AD) 租户的客户的信息。  如果你与其他客户（例如通过[云解决方案提供商 (CSP) 计划](/partner-center/csp-overview)）合作，你不会在“客户”部分看到有关这些客户的信息，除非你已[将其资源加入 Azure Lighthouse](onboard-customer.md)（但是可以在页面下半部分的[云解决方案提供商（预览）部分](#cloud-solution-provider-preview)看到有关特定 CSP 客户的详细信息。

> [!NOTE]
> 你的客户可以通过导航到 Azure 门户中的“我的客户”来查看有关服务提供商的信息。 有关详细信息，请参阅[查看和管理服务提供商](view-manage-service-providers.md)。

## <a name="view-and-manage-customer-details"></a>查看和管理客户详细信息

若要查看客户详细信息，请选择“我的客户”页左侧的“客户”。

> [!IMPORTANT]
> 若要查看此信息，必须在加入过程中向用户授予[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或包括读者访问权限的其他内置角色）。

对于每位客户，你将看到客户姓名、客户 ID（租户 ID）以及与用户参与相关联的产品/服务 ID 和产品/服务版本。  在“委派”列中，你将看到委派订阅数和/或委派资源组数。

通过页面顶部的选项，可以按特定的客户、产品/服务或关键字，对客户信息进行排序、筛选和分组。

可从此页面查看以下信息：

- 若要查看与客户关联的所有订阅、产品/服务和委派，请选择该客户的姓名。
- 若要查看关于产品/服务及其委派的详细信息，请选择产品/服务名称。
- 若要查看关于角色分配的更多详细信息，对于委托订阅或资源组，请选择“委托”列中的条目。

> [!NOTE]
> 如果客户在委托订阅后对其进行重命名，你会看到更新的订阅名称。 如果它他们重命名租户，你可能仍会在 Azure 门户的某些位置看到较早的租户名称。

## <a name="view-and-manage-delegations"></a>查看和管理委派

委派显示已委派的订阅或资源组，以及有权访问该订阅/资源组的用户和权限。 若要查看此信息，请选择“我的客户”页左侧的“委派”。

通过页面顶部的选项，可以按特定的客户、产品/服务或关键字，对信息进行排序、筛选和分组。

### <a name="view-role-assignments"></a>查看角色分配

与每个委派相关联的用户和权限显示在“角色分配”列中。 可选择每个条目以查看已向其授予访问订阅或资源组的权限的用户、组和服务主体的完整列表。 在此处，可选择特定用户、组或服务主体名称以获取详细信息。

### <a name="remove-delegations"></a>删除委派

如果在将客户加入到 Azure Lighthouse 时包含具有[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的用户，则这些用户可以通过选择在委托的行中显示的垃圾桶图标来删除该委派。 执行此操作时，服务提供商租户中的任何用户都无法访问之前委派的资源。

有关详细信息，请参阅[删除对委派的访问权限](remove-delegation.md)。

## <a name="view-delegation-change-activity"></a>查看委派更改活动

每次将客户订阅或资源组委派给租户，以及每次删除之前委派的资源时，“我的客户”页面“活动日志”部分都会进行记录。  只有[在根范围分配到“监视读者”角色](monitor-delegation-changes.md)的用户可以查看此信息。

有关详细信息，请参阅[在 Azure 门户中查看委派更改](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal)。

## <a name="work-in-the-context-of-a-delegated-subscription"></a>在委派订阅的上下文中操作

可直接在 Azure 门户中的委派订阅上下文内进行操作，而无需切换已登录到的目录。 为此，请执行以下操作：

1. 选择 Azure 门户顶部附近的“目录 + 订阅”或“设置”图标 。
1. 在[“目录 + 订阅”设置页](../../azure-portal/set-preferences.md#directories--subscriptions)中，请确保“高级筛选器”切换开关为[关闭状态](../../azure-portal/set-preferences.md#subscription-filters)。
1. 在“默认订阅筛选器”部分，选择合适的目录和订阅。

:::image type="content" source="../media/subscription-filter-delegated.png" alt-text="显示已委托订阅的筛选器的屏幕截图。":::

如果之后访问支持[跨租户管理体验](../concepts/cross-tenant-management-experience.md)的服务，该服务将默认为包含在筛选器中的已委派订阅的上下文。 可通过执行上述步骤并选中“全选”框（或选择要处理的一个或多个订阅），来对此进行更改。

> [!NOTE]
> 如果已向你授予访问一个或多个资源组的权限，而非访问整个订阅的权限，请选择该资源组所属的订阅。 然后，可在该订阅的上下文中操作，但只能访问委派资源组。

还可以选择单个服务中的订阅或资源组，来访问与服务（支持跨租户管理体验）中已委派订阅或资源组相关的功能。

## <a name="cloud-solution-provider-preview"></a>云解决方案提供商（预览）

“我的客户”页上单独的“云解决方案提供商(预览)”部分显示了 [已签署 Microsoft 客户协议 (MCA)](/partner-center/confirm-customer-agreement) 并参与 [Azure 计划](/partner-center/azure-plan-get-started)的 CSP 客户的帐单信息和资源。 有关详细信息，请参阅[开始使用 Microsoft 合作伙伴协议计费帐户](../../cost-management-billing/understand/mpa-overview.md)。

此部分中将显示此类 CSP 客户，无论你是否也将其载入到 Azure Lighthouse。 同样，CSP 客户不一定要显示在“我的客户”的“云解决方案提供商(预览)”部分中，你也可将其加入 Azure Lighthouse。 

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 通过转到 Azure 门户中“服务提供商”来了解你的客户如何[查看和管理服务提供商](view-manage-service-providers.md)。
