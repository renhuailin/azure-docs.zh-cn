---
title: 启用用于路由事件的托管标识（预览版）- CLI
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure CLI 为 Azure 数字孪生启用系统分配的标识，并使用该标识转发事件。
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 272c6e80633da826bf14389fbe0a1d2783d34a3d
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110098663"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>启用用于路由 Azure 数字孪生事件的托管标识（预览版）：Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

本文介绍如何启用 [Azure 数字孪生实例的系统分配的标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)（目前为预览版），并在将事件转发到受支持的目标（例如[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)目标）和 [Azure 存储容器](../storage/blobs/storage-blobs-introduction.md)时使用标识。

本文将逐步介绍如何使用 [Azure CLI](/cli/azure/what-is-azure-cli) 完成此过程。

本文中所涉及的步骤如下： 

1. 创建一个带有系统分配的标识的 Azure 数字孪生实例，或者在现有的 Azure 数字孪生实例上启用系统分配的标识。 
1. 将一个或多个适当的角色添加到该标识。 例如，如果终结点是事件中心，则将“Azure 事件中心数据发送者”角色分配给该标识；如果终结点是服务总线，则将“Azure 服务总线数据发送者”角色分配给该标识。 
1. 在 Azure 数字孪生中创建一个可以使用系统分配的标识进行身份验证的终结点。

## <a name="enable-system-managed-identities-for-an-instance"></a>为实例启用系统托管标识 

当你在 Azure 数字孪生实例上启用系统分配的标识时，Azure 会在 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 中自动为该实例创建一个标识。 然后，可以使用该标识向 Azure 数字孪生终结点进行身份验证，以转发事件。

可以在 **对实例进行初始设置的过程中** 为 Azure 数字孪生实例启用系统托管标识，或者 **稍后在已存在的实例上启用系统托管标识**。

这些创建方法都将为实例提供相同的配置选项和相同的最终结果。 本部分将介绍如何使用这两种方法。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>在创建实例的过程中添加系统托管标识

在本部分，你将了解如何在当前正在创建的 Azure 数字孪生实例上启用系统托管标识。 

这是通过将 `--assign-identity` 参数添加到用于创建实例的 `az dt create` 命令来实现的。 （有关此命令的详细信息，请参阅其[参考文档](/cli/azure/dt#az_dt_create)或[有关设置 Azure 数字孪生实例的一般说明](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)）。

若要创建具有系统托管标识的实例，请按如下所示添加 `--assign-identity` 参数：

```azurecli-interactive
az dt create --dt-name {new_instance_name} --resource-group {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>将系统托管标识添加到现有实例

在本部分，你要将一个系统托管标识添加到已存在的 Azure 数字孪生实例。

也可以使用 `az dt create` 命令和 `--assign-identity` 参数实现此目的。 无需提供要创建的实例的新名称，而可以提供现存实例的名称，以更新该实例的 `--assign-identity` 值。

用于 **启用** 托管标识的命令与创建具有系统托管标识的实例的命令相同。 唯一的差别在于实例名称参数的值：

```azurecli-interactive
az dt create --dt-name {name_of_existing_instance} --resource-group {resource_group} --assign-identity
```

若要在当前已启用托管标识的实例上 **禁用** 托管标识，请使用类似于下面的命令将 `--assign-identity` 设置为 `false`。

```azurecli-interactive
az dt create --dt-name {name_of_existing_instance} --resource-group {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>将 Azure 角色分配给标识 

为 Azure 数字孪生实例创建系统分配的标识后，需要为其分配适当的角色来通过不同类型的[终结点](concepts-route-events.md)进行身份验证，以将事件转发到受支持的目标。 本部分将介绍角色选项，以及如何将它们分配给系统分配的标识。

>[!NOTE]
> 这是一个重要步骤 — 如果不执行此步骤，标识将无法访问终结点，并且事件不会被传送。

### <a name="supported-destinations-and-azure-roles"></a>支持的目标和 Azure 角色 

下面是标识为了访问终结点而需要的最小角色，所需的具体角色取决于目标的类型。 分配权限更高的角色（例如“数据所有者”角色）也同样起作用。

| 目标 | Azure 角色 |
| --- | --- |
| Azure 事件中心 | Azure 事件中心数据发送方 |
| Azure 服务总线 | Azure 服务总线数据发送方 |
| Azure 存储容器 | 存储 Blob 数据参与者 |

有关 Azure 数字孪生中支持用于路由的终结点、路由和目标类型的详细信息，请参阅概念：事件路由。

### <a name="assign-the-role"></a>分配角色

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

可将 `--scopes` 参数添加到 `az dt create` 命令，以将该标识分配到一个或多个具有指定角色的范围。 可以在首次创建实例时使用此方法，也可以稍后通过传入已存在的实例的名称来使用。

以下示例创建一个具有系统托管标识的实例，并在事件中心为该标识分配一个名为 `MyCustomRole` 的自定义角色。

```azurecli-interactive
az dt create --dt-name {instance_name} --resource-group {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

有关使用此命令进行角色分配的更多示例，请参阅 [az dt create 参考文档](/cli/azure/dt#az_dt_create)。

或者，也可以使用 [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) 命令组来创建和管理角色。 此命令可助于支持你不想要使用 create 命令进行组角色分配的其他场景。

## <a name="create-an-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的终结点

为 Azure 数字孪生实例设置系统托管标识并为其分配适当的角色后，可以创建能够使用该标识进行身份验证的 Azure 数字孪生[终结点](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

>[!NOTE]
> 对于已使用基于密钥的标识创建的终结点，不能通过对其进行编辑来将其更改为使用基于标识的身份验证。 必须选择最初创建终结点时指定的身份验证类型。

这是通过将 `--auth-type` 参数添加到用于创建终结点的 `az dt endpoint create` 命令来实现的。 （有关此命令的详细信息，请参阅其[参考文档](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true)或[有关设置 Azure 数字孪生终结点的一般说明](how-to-manage-routes-apis-cli.md#create-the-endpoint)）。

若要创建使用基于标识的身份验证的终结点，请使用 `--auth-type` 参数指定 `IdentityBased` 身份验证类型。 以下示例演示了如何对事件中心终结点使用此参数。

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased --dt-name {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>有关禁用系统托管标识的注意事项

由于标识与使用该标识的终结点是分开管理的，因此必须考虑到对标识或其角色做出的任何更改可能给 Azure 数字孪生实例中的终结点造成的影响。 如果禁用标识或者从中删除某个终结点的必要角色，终结点可能变得不可访问，并且事件流将会中断。

若要继续使用某个终结点，而该终结点是使用现已禁用的托管标识设置的，则需要删除该终结点，然后使用不同的身份验证类型[重新创建它](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)。 做出此项更改后，最长可能需要经过一小时，事件才会继续传送到终结点。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的托管标识： 
* [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)