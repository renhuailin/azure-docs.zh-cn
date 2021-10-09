---
title: Azure Lighthouse 体系结构
description: 了解 Azure Lighthouse 中租户之间的关系，以及在客户租户中创建的支持这种关系的资源。
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 92890199b11da69fac58ec4ff2bccc1ab347d076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629116"
---
# <a name="azure-lighthouse-architecture"></a>Azure Lighthouse 体系结构

Azure Lighthouse 帮助服务提供商简化客户参与和载入体验，同时灵活精确地管理大规模委派资源。 授权用户、组和服务主体可直接在客户订阅的上下文中工作，而无需具有该客户的 Azure Active Directory (Azure AD) 租户中的帐户或成为该客户的租户的共同所有者。 用于支持此访问的机制称为 Azure 委派资源管理。

:::image type="content" source="../media/delegation.jpg" alt-text="说明 Azure 委派资源管理的关系图。":::

> [!TIP]
> 还可在[具有多个其自己的 Azure AD 租户的企业中](enterprise.md)使用 Azure Lighthouse，以简化跨租户管理。

本主题讨论 Azure Lighthouse 中租户之间的关系，以及在客户租户中创建的支持这种关系的资源。

## <a name="delegation-resources-created-in-the-customer-tenant"></a>在客户租户中创建的委派资源

当客户的订阅或资源组加入 Azure Lighthouse 时，将创建两个资源：注册定义和注册分配 。 可以使用 [API 和管理工具](cross-tenant-management-experience.md#apis-and-management-tool-support)访问这些资源，或[在 Azure 门户中](../how-to/view-manage-customers.md)使用它们。

### <a name="registration-definition"></a>注册定义

注册定义包含 Azure Lighthouse 产品/服务的详细信息（管理租户 ID 以及向管理租户中的特定用户、组和/或服务主体分配内置角色的授权）。

在订阅级别为每个委派订阅或在包含委派资源组的每个订阅中创建注册定义。 使用 API 创建注册定义时，需要在订阅级别工作。 例如，使用 Azure PowerShell，需要在创建新的注册定义 ([New-AzManagedServicesDefinition](/powershell/module/az.managedservices/new-azmanagedservicesdefinition)) 之前使用 New-AzureRmDeployment，而不是使用 New-AzureRmResourceGroupDeployment。

### <a name="registration-assignment"></a>注册分配

注册分配将注册定义分配给特定范围，即加入的订阅和/或资源组。

注册分配在每个委派范围中创建，因此它将位于订阅组级别或资源组级别，具体取决于加入的内容。

每个注册分配都必须在订阅级别引用有效的注册定义，将该服务提供商的授权捆绑到委派范围，从而授予访问权限。

## <a name="logical-projection"></a>逻辑投影

Azure Lighthouse 创建从一个租户到另一个租户的资源逻辑投影。 这样，授权服务提供商用户可登录到他们自己的租户，并获得在委派的客户订阅和资源组中工作的授权。 然后，该服务提供商租户中的用户可以代表其客户执行管理操作，而无需登录每个单独的客户租户。

只要该服务提供商租户中的用户、组或服务主体访问客户租户中的资源，Azure 资源管理器就会收到请求。 资源管理器对这些请求进行身份验证，就像对客户自己的租户中的用户发出的请求进行身份验证一样。 对于 Azure Lighthouse，它通过确认客户租户中存在两个资源（注册定义和注册分配）来实现这一点。 如果是这样，资源管理器会根据这些资源定义的信息授权访问权限。

:::image type="content" source="../media/logical-projection.jpg" alt-text="说明 Azure Lighthouse 中逻辑投影的关系图。":::

服务提供商租户中用户的活动会在活动日志中进行跟踪，该日志存储在客户的租户中。 这样，客户便可以[查看所做的更改以及做出更改的人员](../how-to/view-service-provider-activity.md)。

## <a name="how-azure-lighthouse-works"></a>Azure Lighthouse 的工作原理

概括而言，Azure Lighthouse 为管理租户工作的原理如下：

1. 确定你的组、服务主体或用户在管理客户的 Azure 资源时所需的[角色](tenants-users-roles.md#role-support-for-azure-lighthouse)。
2. 通过[向 Azure 市场发布托管服务产品/服务](../how-to/publish-managed-services-offers.md)或 [部署 Azure 资源管理器模板](../how-to/onboard-customer.md)，指定此访问权限并将客户加入 Azure Lighthouse。 此加入过程在客户的租户中创建上述两个资源（注册定义和注册分配）。
3. 客户加入后，授权用户登录你的管理租户，根据你定义的访问权限在指定的客户范围（订阅或资源组）内执行任务。 客户可以查看执行的所有操作，并可以随时删除访问权限。

虽然在大多数情况下，只有一个服务提供商为客户管理特定资源，但客户可以为同一订阅或资源组创建多个委派，从而允许多个服务提供商具有访问权限。 此场景还支持[将资源从服务提供商的租户投射到多个客户](isv-scenarios.md#saas-based-multi-tenant-offerings)的 ISV 场景。

## <a name="next-steps"></a>后续步骤

- 查看 [Azure CLI](/cli/azure/managedservices) 和 [Azure Powershell](/powershell/module/az.managedservices) 命令，以使用注册定义和注册分配。
- 了解适用于 Azure Lighthouse 的[增强的服务和场景](cross-tenant-management-experience.md#enhanced-services-and-scenarios)。
- 详细了解[租户、用户和角色](tenants-users-roles.md)如何使用 Azure Lighthouse。
