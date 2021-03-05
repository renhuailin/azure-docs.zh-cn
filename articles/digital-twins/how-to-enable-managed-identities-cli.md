---
title: " (预览版中为路由事件启用托管标识) -CLI"
titleSuffix: Azure Digital Twins
description: 请参阅如何为 Azure 数字孪生启用系统分配的标识并将其用于转发事件，使用 Azure CLI。
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202954"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>启用托管标识，以将 Azure 数字孪生事件路由 (预览) ： Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

本文介绍了如何为当前处于预览)  (的[Azure 数字孪生实例启用系统分配的标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)，并在将事件转发到受支持的目标（如[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)   目标和[Azure 存储容器](../storage/blobs/storage-blobs-introduction.md)）时使用标识。

本文将逐步讲解如何使用 [**Azure CLI**](/cli/azure/what-is-azure-cli)。

下面是本文中所述的步骤： 

1. 使用系统分配的标识创建 Azure 数字孪生实例，或在现有的 Azure 数字孪生实例上启用系统分配的标识。 
1. 向标识中添加适当的角色或角色。 例如，如果终结点是事件中心，则将 **Azure 事件中心数据发送方** 角色分配给标识; 如果终结点是服务总线，则将其分配给 **Azure 服务总线数据发送方角色** 。
1. 在 Azure 数字孪生中创建一个可以使用系统分配的标识进行身份验证的终结点。

## <a name="enable-system-managed-identities-for-an-instance"></a>为实例启用系统管理的标识 

当你在 Azure 数字孪生实例上启用系统分配的标识时，Azure 会在 [Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md)中自动为其创建标识。 然后，可以使用该标识对 Azure 数字孪生终结点进行身份验证，以便进行事件转发。

可以在 **实例的初始安装过程** 中启用 Azure 数字孪生实例的系统管理的标识，也可以 **稍后在已存在的实例上启用它**。

这些创建方法中的任何一种都将为你的实例提供相同的配置选项和相同的最终结果。 本部分介绍如何执行这两项操作。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>在实例创建过程中添加系统管理的标识

在本部分中，你将了解如何在当前创建的 Azure 数字孪生实例上启用系统管理的标识。 

这是通过将参数添加 `--assign-identity` 到 `az dt create` 用于创建实例的命令来完成的。  (有关此命令的详细信息，请参阅本 [文档的参考文档](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) 或 [设置 Azure 数字孪生实例的常规说明](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)) 。

若要创建具有系统托管标识的实例，请添加  `--assign-identity` 参数，如下所示：

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>向现有实例添加系统管理的标识

在本部分中，会将系统管理的标识添加到已存在的 Azure 数字孪生实例。

还可以通过 `az dt create` 命令和参数完成此操作 `--assign-identity` 。 您可以提供已存在的实例的名称，以更新该实例的值，而不是提供要创建的实例的新名称 `--assign-identity` 。

用于 **启用** 托管标识的命令与用系统托管标识创建实例的命令相同。 所有这些更改都是 instance name 参数的值：

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

若要对当前启用的实例 **禁用** 托管标识，请使用以下类似的命令将设置 `--assign-identity` 为 `false` 。

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>将 Azure 角色分配到标识 

为 Azure 数字孪生实例创建系统分配的标识后，需要为其分配适当的角色以使用不同类型的 [终结点](concepts-route-events.md) 进行身份验证，以便将事件转发到受支持的目标。 本部分介绍角色选项，以及如何将它们分配到系统分配的标识。

>[!NOTE]
> 这是一个重要的步骤，如果没有它，标识将无法访问终结点，并且不会传递事件。

### <a name="supported-destinations-and-azure-roles"></a>支持的目标和 Azure 角色 

下面是标识访问终结点所需的最少角色，具体取决于目标的类型。 具有更高权限的角色 (如数据所有者角色) 也将起作用。

| 目标 | Azure 角色 |
| --- | --- |
| Azure 事件中心 | Azure 事件中心数据发送方 |
| Azure 服务总线 | Azure 服务总线数据发送方 |
| Azure 存储容器 | 存储 Blob 数据参与者 |

有关终结点、路由和 Azure 数字孪生中支持路由的目标类型的详细信息，请参阅 [*概念：事件路由*](concepts-route-events.md)。

### <a name="assign-the-role"></a>分配角色

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

您可以将参数添加到 `--scopes` `az dt create` 命令中，以便将该标识分配给一个或多个具有指定角色的作用域。 这可以在首次创建实例时使用，也可以在以后通过传入已经存在的实例的名称来使用。

下面的示例使用系统管理的标识创建一个实例，并为该标识分配一个 `MyCustomRole` 在事件中心中调用的自定义角色。

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

有关此命令的角色分配的更多示例，请参阅 [ **az dt create** reference 文档](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create)。

或者，也可以使用 [**az role 赋值**](/cli/azure/role/assignment) 命令组来创建和管理角色。 这可用于支持不想使用 create 命令对角色分配进行分组的其他方案。

## <a name="create-an-endpoint-with-identity-based-authentication"></a>创建具有基于标识的身份验证的终结点

为 Azure 数字孪生实例设置系统管理的标识并向其分配适当的角色 () ，你可以创建能够使用标识进行身份验证的 Azure 数字孪生 [终结点](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 。 此选项仅适用于事件中心和服务总线类型终结点， (事件网格) 不支持此选项。

>[!NOTE]
> 不能编辑已使用基于密钥的标识更改为基于标识的身份验证的终结点。 首次创建终结点时，必须选择身份验证类型。

这是通过将参数添加 `--auth-type` 到 `az dt endpoint create` 用于创建终结点的命令来完成的。  (有关此命令的详细信息，请参阅有关[设置 Azure 数字孪生终结点](how-to-manage-routes-apis-cli.md#create-the-endpoint)[的详细](/cli/azure/ext/azure-iot/dt/endpoint/create)信息) 。

若要创建使用基于标识的身份验证的终结点，请 `IdentityBased` 使用参数指定身份验证类型  `--auth-type` 。 下面的示例演示了事件中心终结点的这种情况。

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>禁用系统托管标识的注意事项

由于标识是与使用它的终结点分开管理的，因此请务必考虑对标识或其角色所做的任何更改对 Azure 数字孪生实例中的终结点的影响。 如果已禁用标识，或者删除了某个终结点的必要角色，则终结点可能变为不可访问，并且事件流将中断。

若要继续使用通过已禁用的托管标识设置的终结点，则需要删除终结点，并使用不同的身份验证类型 [重新创建它](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) 。 在此更改后，事件可能需要长达一小时的时间才能继续传递到终结点。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的托管标识： 
* [*Azure 资源的托管标识*](../active-directory/managed-identities-azure-resources/overview.md)