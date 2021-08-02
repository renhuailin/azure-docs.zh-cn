---
title: 查看和管理服务提供商
description: 客户可以在 Azure 门户中查看有关 Azure Lighthouse 服务提供商、服务提供商产品/服务和委派资源的信息。
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: 23fe84132b3c2dbee584f452b14503adfdb6db7d
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112080914"
---
# <a name="view-and-manage-service-providers"></a>查看和管理服务提供商

客户可通过 [Azure 门户](https://portal.azure.com)中的“服务提供商”页控制和查看使用 [Azure Lighthouse](../overview.md) 的服务提供商。 客户可以查看有关服务提供商的详细信息、委派特定资源、查找新产品/服务，以及删除服务提供商访问权限等。

要查看 Azure 门户中的“服务提供者”页面，选择“所有服务”，然后搜索“服务提供商”并选择它。 还可以通过在 Azure 门户顶部附近的搜索框中输入“服务提供商”或“Azure Lighthouse”来找到此页。

> [!NOTE]
> 若要查看“服务提供商”页，客户租户中的用户必须具有[读者内置角色](../../role-based-access-control/built-in-roles.md#reader)（或其他包含读者访问权限的内置角色）。
>
> 若要添加或更新产品/服务、委派资源和删除产品/服务，用户必须具有拥有 `Microsoft.Authorization/roleAssignments/write` 权限的角色，如[所有者](../../role-based-access-control/built-in-roles.md#owner)。

请记住，“服务提供商”页面仅显示有关可通过 Azure Lighthouse 访问客户的订阅或资源组的服务提供商的信息。 如果客户与不使用 Azure Lighthouse 的其他服务提供商合作，则此处不会显示有关这些服务提供商的信息。

## <a name="view-service-provider-details"></a>查看服务提供商详情

若要查看有关使用 Azure Lighthouse 来处理客户租户的当前服务提供商的详细信息，请在“服务提供商”页面左侧选择“服务提供商产品/服务”。

对于每个产品/服务，你都将看到服务提供商的名称和与之关联的产品/服务。 可以选择一个产品/服务来查看描述和其他详细信息，包括已授予服务提供商的角色分配。

在“委派”列中，可看到针对该产品/服务已委派给服务提供商的订阅数和/或资源组数。 服务提供商将能够根据产品/服务中指定的访问级别访问和管理这些订阅和/或资源组。

## <a name="add-or-remove-service-provider-offers"></a>添加或删除服务提供商产品/服务

若要从“服务提供商产品/服务”中添加新的服务提供商产品/服务，请选择“添加产品/服务”。 选择“专有产品/服务”以查看服务提供商为该客户发布的产品/服务。 然后可以从“专有产品/服务”屏幕中选择该产品/服务，并选择“设置 + 订阅”。

可以通过选择该产品/服务行中的“回收站”图标随时删除服务提供商的产品/服务。 确认删除后，该服务提供商将无法再访问之前为该产品/服务委派的资源。

## <a name="delegate-resources"></a>委派资源

在服务提供商可以访问和管理客户资源之前，必须委派一个或多个特定的订阅和/或资源组。 如果客户已接受产品/服务但尚未委派任何资源，则他们会在“服务提供商产品/服务”部分的顶部看到备注。 这让客户知道他们需要在服务提供商可以访问客户的任何资源之前采取措施。

委派订阅或资源组：

1. 选中包含服务提供商、产品/服务和名称的行的复选框。 然后选择屏幕顶部的“委派资源”。
1. 在“委派资源”页面的“产品/服务详细信息”部分中，查看有关服务提供商和产品/服务的详细信息。 要查看产品/服务的角色分配，请选择“单击此处查看所选产品/服务的详情信息”。
1. 在“委派”部分中，选择“委派订阅”或“委派资源组”。
1. 选择要为此产品/服务委派的订阅和/或资源组，然后选择“添加”。
1. 选中页面底部的复选框，确认你想要授予此服务提供商对所选资源的访问权限，然后选择“委派”。

## <a name="update-service-provider-offers"></a>更新服务提供商提供的服务

在客户添加了产品/服务后，服务提供商可以将同一产品/服务的更新版本发布到 Azure Marketplace。 例如，他们可能要添加新的角色定义。 如果发布了产品/服务的新版本，则“服务提供商产品/服务”页面将在该产品/服务的行中显示“更新”图标。 选择此图标来查看当前版本的产品/服务和新版本的产品/服务之间的差异。

 ![更新产品/服务图标](../media/update-offer.jpg)

查看更改后，客户可以选择是否更新为新版本。 在新版本中指定的授权和其他设置随后将应用于已为该产品/服务委派的所有订阅和/或资源组。

## <a name="view-delegations"></a>查看委派

委派表示为客户委派的资源授予服务提供者权限的角色分配。 若要查看此信息，请选择“服务提供程序”页面左侧的“委派”。

可以通过页面顶部的筛选器对委派信息进行排序和分组。 还可以按特定客户、产品/服务或关键字进行筛选。

> [!NOTE]
> 在 [Azure 门户中查看委派范围的角色分配](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) 或通过 API 查看时，客户不会看到这些角色分配，或服务提供商租户中已被授予这些角色的任何用户。

## <a name="audit-delegations-in-your-environment"></a>审核环境中的委派

客户可能需要了解已委派给 Azure Lighthouse 的订阅和/或资源组。 对于具有大量订阅的客户，或者拥有执行管理任务的多个用户的客户，此操作特别有用。

我们提供了 [Azure Policy 内置策略定义](../../governance/policy/samples/built-in-policies.md#lighthouse)，用于[审核范围限定到管理租户的委派](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)。 可以将此策略分配到包含要审核的所有订阅的管理组。 检查此策略的符合性时，任何委派订阅和/或资源组（在策略分配到的管理组中）将显示为不符合状态。 然后，可以查看结果，并确认没有任何意外委派。

另一个[内置策略定义](../../governance/policy/samples/built-in-policies.md#lighthouse)使你能够[将委派限制到特定的管理租户](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json)。 此策略同样可以应用于包含任何要限制委派的订阅的管理组。 部署策略后，任何尝试将订阅委派给指定租户以外的租户的行为都将被拒绝。

有关如何分配策略和查看符合性状态结果的详细信息，请参阅[快速入门：创建策略分配](../../governance/policy/assign-policy-portal.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Lighthouse ](../overview.md)。
- 了解如何[审核服务提供商活动](view-service-provider-activity.md)。
- 了解服务提供商如何在 Azure 门户的“我的客户”页面上[查看和管理客户](view-manage-customers.md)。
- 了解[管理多个租户的企业](../concepts/enterprise.md)如何使用 Azure Lighthouse 来合并其管理体验。

