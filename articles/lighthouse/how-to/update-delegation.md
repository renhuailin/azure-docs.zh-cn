---
title: 更新委派
description: 了解如何为以前载入到 Azure Lighthouse 的客户更新委派。
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: 90827281ac9b05105700298494af0b60b0fa511f
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96610889"
---
# <a name="update-a-delegation"></a>更新委派

在载入订阅 (或) 到 Azure Lighthouse 的资源组后，你可能需要进行更改。 例如，你的客户可能希望执行需要不同 Azure 内置角色的其他管理任务，或者可能需要更改向其委派客户订阅的租户。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但 [管理多个租户的企业](../concepts/enterprise.md) 可以使用相同的过程来设置 Azure Lighthouse 并合并其管理体验。

如果 [通过 Azure 资源管理器 (ARM) 模板将客户载入](onboard-customer.md)，则必须为该客户执行新的部署。 根据你要更改的内容，你可能需要更新原始产品/服务，或者删除原始产品/服务并创建一个新产品/服务。

- **如果只更改授权**：可以通过仅更改 ARM 模板的 " **授权** " 部分来更新委托。
- **如果要更改管理租户**：必须使用与以前的产品/服务不同的 **mspOfferName** 创建新的 ARM 模板。

## <a name="update-your-arm-template"></a>更新 ARM 模板

若要更新委派，你将需要部署包含要进行的更改的 ARM 模板。

如果仅更新授权 (例如，添加具有以前未包含的角色的新用户组，或更改现有用户) 的角色，则可以使用 [ARM 模板](onboard-customer.md#create-an-azure-resource-manager-template)中的相同 **mspOfferName** ，用于上一次委派。 您可以使用以前的模板作为起点。 然后，进行所需的更改，如将一个 Azure 内置角色替换为其他角色，或者向该模板添加全新的授权。

如果更改了 **mspOfferName**，则会将其视为新的单独提议。 如果要更改管理租户，则需要进行此更改。

如果管理租户保持不变，则不需要更改 **mspOfferName** 。 在大多数情况下，我们建议使用同一客户和管理租户只使用一个 **mspOfferName** 。 如果你选择继续进行更改，请确保在部署新委托之前删除客户的以前的委托。

## <a name="remove-the-previous-delegation"></a>删除以前的委托

在执行新部署之前，您可能需要 [删除对以前委派的访问权限](remove-delegation.md)。 这可以确保删除所有以前的权限，从而使你能够使用应向前应用的确切用户/组和角色开始使用。

> [!IMPORTANT]
> 如果使用新的 **mspOfferName** 并保留任何相同的 **principalId** 值，则必须先删除对以前委派的访问权限，然后再部署新产品/服务。 如果不先删除该产品/服务，则以前授予了权限的用户可能会因为分配冲突而完全丢失访问权限。

如果你要更改管理租户，并且你希望这两个租户继续具有访问权限，则可以保留以前的产品/服务。 如果只希望新的管理租户具有访问权限，则必须删除以前的产品/服务。 这可以在载入新产品/服务之前或之后执行。

如果要更新产品/服务以仅调整授权，并保持相同的 **mspOfferName**，则无需删除以前的委托。 新部署将替换以前的委托，并且仅应用最新模板中的授权。

:::image type="content" source="../media/update-delegation.jpg" alt-text="显示何时更改 mspOfferName 并删除以前的委托的关系图。":::

如果管理租户中的任何用户已向其授予 [托管服务注册分配 "删除" 角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ，则可以对该委托执行删除访问权限。 如果管理租户中的任何用户都没有此角色，你可以要求客户 [删除 Azure 门户中的产品/服务的访问权限](view-manage-service-providers.md#add-or-remove-service-provider-offers)。

> [!TIP]
> 如果已按照上述步骤删除了之前的委派，但仍无法部署新的 ARM 模板，则可能需要 [完全删除注册定义](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)。 此操作可由客户租户中拥有 "所有者" 角色的任何用户执行。  

## <a name="deploy-the-arm-template"></a>部署 ARM 模板

你的客户可以采用与之前相同的方式 [部署已更新的模板](onboard-customer.md#deploy-the-azure-resource-manager-templates) ：在 Azure 门户中，使用 PowerShell，或使用 Azure CLI。

部署完成后，请 [确认它已成功](onboard-customer.md#confirm-successful-onboarding)完成。 更新后的授权将对客户已委派的订阅或资源组 () 有效。

## <a name="updating-managed-service-offers"></a>正在更新托管服务产品

如果你通过发布到 Azure Marketplace 的托管服务产品/服务载入客户，并且想要更新授权，则可以通过 [发布新版本的产品](../../marketplace/partner-center-portal/update-existing-offer.md) /服务，并在计划中为该客户使用已更新的 [授权](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) ，来更新委派。 然后，客户将能够更新到 Azure 门户中的最新版本。

如果要更改管理租户，则需要 [创建并发布新的托管服务产品](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) 以供客户接受。

> [!TIP]
> 如前文所述，我们建议你不要在同一客户和管理租户之间使用多个不同的产品/服务。 如果为使用同一管理租户的同一客户发布新产品/服务，请确保在客户接受新的产品/服务之前删除以前的产品/服务。

## <a name="next-steps"></a>后续步骤

- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
- 了解如何[删除对以前加入的委托的访问权限](remove-delegation.md)。
