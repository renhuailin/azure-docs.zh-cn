---
title: 使用托管标识路由事件
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure 门户或 CLI 为 Azure 数字孪生启用系统分配的标识，并使用该标识转发事件。
author: baanders
ms.author: baanders
ms.date: 6/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: subject-rbac-steps, contperf-fy21q4
ms.openlocfilehash: 4d50c40426d5fb687b28a965b9d921ef6fc4df38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651899"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events"></a>启用用于路由 Azure 数字孪生事件的托管标识

本文介绍如何启用 [Azure 数字孪生实例的系统分配的标识](concepts-security.md#managed-identity-for-accessing-other-resources)，并在将事件转发到受支持的路由目标时使用该标识。 虽然路由不强制要求设置托管标识，但它可以帮助实例轻松访问其他受 Azure AD 保护的资源，例如[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md) 目标和 [Azure 存储容器](../storage/blobs/storage-blobs-introduction.md)。

本文中所涉及的步骤如下： 

1. 创建一个带有系统分配的标识的 Azure 数字孪生实例，或者在现有的 Azure 数字孪生实例上启用系统分配的标识。 
1. 将一个或多个适当的角色添加到该标识。 例如，如果终结点是事件中心，则将“Azure 事件中心数据发送者”角色分配给该标识；如果终结点是服务总线，则将“Azure 服务总线数据发送者”角色分配给该标识。 
1. 在 Azure 数字孪生中创建一个可以使用系统分配的标识进行身份验证的终结点。

## <a name="enable-system-managed-identity-for-the-instance"></a>为实例启用系统托管标识 

当你在 Azure 数字孪生实例上启用系统分配的标识时，Azure 会在 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 中自动为该实例创建一个标识。 然后，可以使用该标识向 Azure 数字孪生终结点进行身份验证，以转发事件。

可以在 **对实例进行初始设置的过程中** 为 Azure 数字孪生实例启用系统托管标识，或者 **稍后在已存在的实例上启用系统托管标识**。

这些创建方法都将为实例提供相同的配置选项和相同的最终结果。 本部分将介绍如何使用这两种方法。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>在创建实例的过程中添加系统托管标识

在本部分，你将了解如何在创建 Azure 数字孪生实例时，在该实例上启用系统托管标识。 无论是使用 [Azure 门户](https://portal.azure.com)还是 [Azure CLI](/cli/azure/what-is-azure-cli) 创建实例，都可以启用标识。 使用下面的选项卡选择首选体验的说明。

# <a name="portal"></a>[Portal](#tab/portal) 

要在门户中创建实例时添加托管标识，请先[按常规方式创建实例](how-to-set-up-instance-portal.md)。

系统托管标识选项位于实例设置的“高级”选项卡中。

在此选项卡中，选择“系统托管标识”对应的“打开”选项以打开此功能。 

:::image type="content" source="media/how-to-route-with-managed-identity/create-instance-advanced.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure 数字孪生的“创建资源”对话框的“高级”选项卡。系统托管标识已启用。":::

然后，可以使用底部的导航按钮继续完成余下的实例设置。
   
# <a name="cli"></a>[CLI](#tab/cli)

在 CLI 中，可将 `--assign-identity` 参数添加到用于创建实例的 `az dt create` 命令中。 （有关此命令的详细信息，请参阅其[参考文档](/cli/azure/dt#az_dt_create)或[有关设置 Azure 数字孪生实例的一般说明](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)）。

若要创建具有系统托管标识的实例，请按如下所示添加 `--assign-identity` 参数：

```azurecli-interactive
az dt create --dt-name <new-instance-name> --resource-group <resource-group> --assign-identity
```

---

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>将系统托管标识添加到现有实例

在本部分，你要将一个系统托管标识添加到已存在的 Azure 数字孪生实例。 使用下面的选项卡选择首选体验的说明。

# <a name="portal"></a>[Portal](#tab/portal) 

首先，在浏览器中打开 [Azure 门户](https://portal.azure.com)。

1. 在门户搜索栏中搜索你的实例名称，然后选择该实例查看其详细信息。

1. 在左侧菜单中选择“标识”。

1. 在此页上，选择“打开”选项以打开此功能。

1. 选择“保存”按钮，然后点击“是”以确认 。

    :::image type="content" source="media/how-to-route-with-managed-identity/identity-digital-twins.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure 数字孪生实例的“标识”页。":::

保存更改后，此页将显示新标识的更多字段：“对象 ID”和“权限”。 

如果需要，可以复制此处的“对象 ID”，并使用“权限”按钮查看分配给该标识的 Azure 角色。  若要设置一些角色，请继续阅读下一部分。

# <a name="cli"></a>[CLI](#tab/cli)

同样，可使用 `az dt create` 命令和 `--assign-identity` 参数将标识添加到实例中。 无需提供要创建的实例的新名称，而可以提供现存实例的名称，以更新该实例的 `--assign-identity` 值。

用于 **启用** 托管标识的命令与创建具有系统托管标识的实例的命令相同。 唯一的差别在于实例名称参数的值：

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity
```

若要在当前已启用托管标识的实例上 **禁用** 托管标识，请使用类似于下面的命令将 `--assign-identity` 设置为 `false`。

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity false
```

---

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

要详细了解 Azure 数字孪生中支持用于路由的终结点、路由和目标类型，请参阅[事件路由](concepts-route-events.md)。

### <a name="assign-the-role"></a>分配角色

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

使用下面的选项卡选择首选体验的说明。

# <a name="portal"></a>[Portal](#tab/portal) 

要为标识分配角色，请先在浏览器中打开 [Azure 门户](https://portal.azure.com)。

1. 在门户搜索栏中搜索你的终结点资源（事件中心、服务总线主题或存储容器）名称并导航到该资源。 

1. 选择“访问控制 (IAM)”。

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 使用以下信息将所需角色分配给 Azure 数字孪生实例的托管标识。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 从下拉菜单中选择所需的角色。 |
    | 将访问权限分配到 | 在“系统分配的托管标识”下，选择“数字孪生” 。 |
    | 成员 | 在此处选择要为其分配角色的 Azure 数字孪生实例的托管标识。 托管标识的名称与实例的名称相匹配，因此请选择你的 Azure 数字孪生实例的名称。 |
    
    ![“添加角色分配”页](../../includes/role-based-access-control/media/add-role-assignment-page.png)

# <a name="cli"></a>[CLI](#tab/cli)

可将 `--scopes` 参数添加到 `az dt create` 命令，将该标识分配到一个或多个具有指定角色的范围。 你可在首次创建实例时使用这个带有此参数的命令，也可稍后通过传入已存在的实例的名称来使用。

以下示例创建一个具有系统托管标识的实例，并在事件中心为该标识分配一个名为 `MyCustomRole` 的自定义角色。

```azurecli-interactive
az dt create --dt-name <instance-name> --resource-group <resource-group> --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource-group>/providers/Microsoft.EventHub/namespaces/<Event-Hubs-namespace>/eventhubs/<event-hub-name>" --role MyCustomRole
```

有关使用此命令进行角色分配的更多示例，请参阅 [az dt create 参考文档](/cli/azure/dt#az_dt_create)。

也可使用 [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) 命令组来创建和管理角色。 此命令可用于支持你不想使用 create 命令进行组角色分配的其他场景。

---

## <a name="create-an-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的终结点

为 Azure 数字孪生实例设置系统托管标识并为其分配适当的角色后，可以创建能够使用该标识进行身份验证的 Azure 数字孪生[终结点](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

>[!NOTE]
> 对于已使用基于密钥的标识创建的终结点，不能通过对其进行编辑来将其更改为使用基于标识的身份验证。 必须选择最初创建终结点时指定的身份验证类型。

使用下面的选项卡选择首选体验的说明。

# <a name="portal"></a>[Portal](#tab/portal) 

请按照[有关创建 Azure 数字孪生终结点的说明](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)开始操作。

在执行填写终结点类型所需的详细信息的步骤时，请务必选择“基于标识”作为“身份验证类型”。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="创建“事件中心”类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

完成终结点设置，然后选择“保存”。

# <a name="cli"></a>[CLI](#tab/cli)

使用 CLI 创建终结点这项操作是通过将 `--auth-type` 参数添加到用于创建终结点的 `az dt endpoint create` 命令来实现的。 （有关此命令的详细信息，请参阅其[参考文档](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true)或[有关设置 Azure 数字孪生终结点的一般说明](how-to-manage-routes.md#create-the-endpoint)）。

若要创建使用基于标识的身份验证的终结点，请使用 `--auth-type` 参数指定 `IdentityBased` 身份验证类型。 以下示例演示了如何对事件中心终结点使用此功能。

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <endpoint-name> --eventhub-resource-group <eventhub-resource-group> --eventhub-namespace <eventhub-namespace> --eventhub <eventhub-name> --auth-type IdentityBased --dt-name <instance-name>
```

---

## <a name="considerations-for-disabling-system-managed-identities"></a>有关禁用系统托管标识的注意事项

由于标识与使用该标识的终结点是分开管理的，因此必须考虑到对标识或其角色做出的任何更改可能给 Azure 数字孪生实例中的终结点造成的影响。 如果禁用标识或者从中删除某个终结点的必要角色，终结点可能变得不可访问，并且事件流将会中断。

若要继续使用某个终结点，而该终结点是使用现已禁用的托管标识设置的，则需要删除该终结点，然后使用不同的身份验证类型[重新创建它](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)。 做出此项更改后，最长可能需要经过一小时，事件才会继续传送到终结点。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的托管标识： 
* [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)