---
title: 更新委派
description: 了解如何为以前加入到 Azure Lighthouse 的客户更新委托。
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: e8044732cd1cb544515bcfe5b2c0ac3b40bf0cfe
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389248"
---
# <a name="update-a-delegation"></a>更新委派

在将订阅（或资源组）加入到 Azure Lighthouse 后，可能会需要进行更改。 例如，你的客户可能希望执行其他需要不同 Azure 内置角色的管理任务，或者你可能需要更改将客户订阅委托到的租户。

> [!TIP]
> 虽然我们在本主题中提到的是服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)可以使用相同的过程来设置 Azure Lighthouse 并整合其管理体验。

如果[通过 Azure 资源管理器模板（ARM 模板）加入客户](onboard-customer.md)，则必须为该客户执行新部署。 根据你要更改的内容，你可能需要更新原始产品/服务，或者删除原始产品/服务并创建新产品/服务。

- 如果只更改授权：可通过只更改 ARM 模板的“授权”部分来更新委托 。
- 如果要更改管理租户：必须使用与以前的产品/服务不同的 mspOfferName 来创建新 ARM 模板 。

## <a name="update-your-arm-template"></a>更新 ARM 模板

若要更新委托，你将需要部署包含要进行的更改的 ARM 模板。

如果只更新授权（例如，添加具有以前未包含的角色的新用户组，或更改现有用户的角色），可使用与上次委托所用 [ARM 模板](onboard-customer.md#create-an-azure-resource-manager-template)相同的 mspOfferName。 可以使用上一个模板作为起始点。 然后，进行所需更改，例如，将一个 Azure 内置角色替换为其他角色，或者为该模板添加全新授权。

如果更改 mspOfferName，则会将其视为新的单独产品/服务。 如果要更改管理租户，则需要进行此更改。

如果管理租户保持不变，则不必更改 mspOfferName。 在大多数情况下，建议对同一客户和管理租户只使用一个 mspOfferName。 如果选择仍继续进行更改，请确保在部署新委托之前先删除该客户的前一次委托。

## <a name="remove-the-previous-delegation"></a>删除以前的委托

在执行新部署之前，可能需要[删除对以一次委托的访问权限](remove-delegation.md)。 这样会确保删除所有以前的权限，使你能够准确地使用那些应该向前应用的用户/组和角色来干净地开始。

> [!IMPORTANT]
> 如果使用新 mspOfferName 并保留任何相同的 principalId 值，则必须先删除对前一委托的访问权限，然后再部署新产品/服务 。 如果不先删除该产品/服务，则以前的已授权用户可能会由于分配冲突而完全丢失访问权限。

如果要更改管理租户，并希望两个租户都继续具有访问权限，则可保留前一产品/服务。 如果只希望新管理租户有访问权限，则必须删除以前的产品/服务。 此操作可以在加入新产品/服务之前或之后执行。

如果更新产品/服务只是为了调整授权，并且保持相同的 mspOfferName，则无需删除前一委托。 新部署将会替换前一委托，并且只有最新模板中的授权才会应用。

:::image type="content" source="../media/update-delegation.jpg" alt-text="显示何时更改 mspOfferName 并删除前一委托的关系图。":::

如果管理租户中的任何用户在原委托中被授予了[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，则该用户可执行对该委托访问权限的删除。 如果管理租户中的任何用户都没有此角色，你可以要求客户[在 Azure 门户中删除产品/服务的访问权限](view-manage-service-providers.md#remove-service-provider-offers)。

> [!TIP]
> 如果已按上述步骤删除了前一委托，但仍无法部署新 ARM 模板，则可能需要[完全删除注册定义](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)。 此操作可由客户租户中任何具有 `Microsoft.Authorization/roleAssignments/write` 权限的角色（如[所有者](../../role-based-access-control/built-in-roles.md#owner)）的用户来完成。  

## <a name="deploy-the-arm-template"></a>部署 ARM 模板

你的客户可以采用与他们以前所用方式相同的方式来[部署更新的模板](onboard-customer.md#deploy-the-azure-resource-manager-template)：在 Azure 门户中、通过使用 PowerShell，或者通过使用 Azure CLI。

在完成了部署之后，请[确认该部署已成功](onboard-customer.md#confirm-successful-onboarding)。 然后，更新后的授权将会对客户已委托的订阅或资源组生效。

## <a name="updating-managed-service-offers"></a>更新托管服务产品/服务

如果你通过发布到 Azure 市场的托管服务产品/服务加入了客户，并且需要更新授权，则可通过使用要在该客户的计划中使用的[授权](../../marketplace/plan-managed-service-offer.md)来[发布新版本的产品/服务](../../marketplace/update-existing-offer.md)，从而更新委托。 然后，客户将能够在 Azure 门户中更新到该最新版本。

如果要更改管理租户，则需要[创建并发布新的托管服务产品/服务](../../marketplace/plan-managed-service-offer.md)以供客户接受。

> [!TIP]
> 如前文所述，建议不在同一客户和管理租户之间使用多个不同的产品/服务。 如果为使用同一管理租户的同一客户发布新产品/服务，请确保在客户接受新的产品/服务之前先删除以前的产品/服务。

## <a name="next-steps"></a>后续步骤

- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
- 了解如何[删除对以前加入的委托的访问权限](remove-delegation.md)。
- 详细了解 [Azure Lighthouse 体系结构](../concepts/architecture.md)。