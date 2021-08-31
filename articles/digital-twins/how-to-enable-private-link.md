---
title: 使用专用链接实现专用访问（预览）
titleSuffix: Azure Digital Twins
description: 了解如何使用专用链接实现对 Azure 数字孪生解决方案的专用访问。
author: baanders
ms.author: baanders
ms.date: 7/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 93435355a2d75a2346d076c44b377726789431a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452328"
---
# <a name="enable-private-access-with-private-link-preview"></a>使用专用链接实现专用访问（预览）

本文介绍[为 Azure 数字孪生实例启用包含专用终结点的专用链接](concepts-security.md#private-network-access-with-azure-private-link-preview)的不同方式（当前为预览版）。 通过为 Azure 数字孪生实例配置专用终结点，可以保护 Azure 数字孪生实例，消除曝光危险，以及避免 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 数据外泄。

本文介绍了以下步骤： 
1. 打开专用链接，并为 Azure 数字孪生实例配置专用终结点。
1. 查看、编辑或删除实例中的专用终结点。
1. 禁用或启用公用网络访问标志，以将 API 访问限制为仅使用专用链接连接。

## <a name="prerequisites"></a>先决条件

在设置专用终结点之前，需要一个可在其中部署终结点的 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 。 如果尚无 VNet，可以按照 [Azure 虚拟网络快速入门](../virtual-network/quick-create-portal.md)中的其中一种方式进行设置。

## <a name="add-a-private-endpoint-to-azure-digital-twins"></a>向 Azure 数字孪生添加专用终结点 

可以使用 [Azure 门户](https://portal.azure.com)或 [Azure CLI](/cli/azure/what-is-azure-cli) 启用与 Azure 数字孪生实例的专用终结点的专用链接。 

如果要在实例的初始设置过程中设置专用链接，需要使用 Azure 门户。 如果要在创建实例后启用其专用链接，可以使用 Azure 门户或 Azure CLI。 每种创建方法都将为实例提供相同的配置选项和相同的最终结果。

使用本部分中的选项卡，可以选择针对你首选体验的说明。

>[!TIP]
> 此外，还可以通过专用链接服务设置专用链接终结点，而非通过 Azure 数字孪生实例。 此方法也将提供相同的配置选项和相同的最终结果。
>
> 有关设置专用链接资源的更多详细信息，请参阅 [Azure 门户](../private-link/create-private-endpoint-portal.md)、[Azure CLI](../private-link/create-private-endpoint-cli.md)、[Azure 资源管理器](../private-link/create-private-endpoint-template.md)或 [PowerShell](../private-link/create-private-endpoint-powershell.md) 的专用链接文档。

### <a name="add-a-private-endpoint-during-instance-creation"></a>在实例创建过程中添加专用终结点

在本部分中，你将创建一个专用终结点，其中会在 Azure 数字孪生实例的初始设置过程中设置专用链接。 这个操作只能通过 Azure 门户来完成。

# <a name="portal"></a>[Portal](#tab/portal)

本部分介绍在 Azure 门户中设置 Azure 数字孪生实例时，如何启用专用链接。 

“专用链接”选项位于实例设置的“网络”选项卡中。

1. 开始在 Azure 门户中设置一个 Azure 数字孪生实例。 有关说明，请参阅[设置实例和身份验证](how-to-set-up-instance-portal.md)。
1. 当你访问实例设置的“网络”选项卡时，可以通过在“连接方式”中选择“专用终结点”选项来启用专用终结点。

    此操作将会添加一个名为“专用终结点连接”的部分，在其中可以配置专用终结点的详细信息。 选择“+ 添加”按钮继续操作。
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure 门户的屏幕截图，其中显示了新 Azure 数字孪生实例的“网络”选项卡，突出显示了如何创建专用终结点。“添加”按钮突出显示。" lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

1. 在打开的“创建专用终结点”页中，输入新专用终结点的详细信息。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Azure 门户的屏幕截图，其中显示“创建专用终结点”页。它包含下面所述的字段。":::

    1. 为“订阅”和“资源组”填充所选内容。 将“位置”设置为与要使用的 VNet 相同的位置。 选择终结点“名称”，并为“目标子资源”选择“API”。

    1. 接下来，选择要用于部署终结点的“虚拟网络”和“子网” 。

    1. 最后，选择是否 **与专用 DNS 区域集成**。 可以使用默认值“是”，或者为了便于选择，通过门户中的链接来[详细了解专用 DNS 集成](../private-link/private-endpoint-overview.md#dns-configuration)。

    1. 填写完配置选项后，选择“确定”完成此操作。

1. 这会返回到 Azure 数字孪生实例设置的“网络”选项卡。 验证新终结点在“专用终结点连接”下是否可见。
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure 门户的屏幕截图，其中显示具有新创建的专用终结点的 Azure 数字孪生的“网络”选项卡。" lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

1. 使用底部的导航按钮继续完成余下的实例设置。

# <a name="cli"></a>[CLI](#tab/cli)

在创建实例期间，不能使用 Azure CLI 添加专用链接终结点。 

你可以切换到 Azure 门户，在实例创建过程中添加终结点，也可以前往下一部分内容，在创建实例后使用 CLI 添加专用终结点。

--- 

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>将专用终结点添加到现有实例

在本部分中，将在已存在的 Azure 数字孪生实例上启用包含专用终结点的专用链接。

# <a name="portal"></a>[Portal](#tab/portal)

1. 首先，在浏览器中导航到 [Azure 门户](https://portal.azure.com)。 在门户搜索栏中搜索 Azure 数字孪生实例的名称，打开该实例。

1. 在左侧菜单中选择“网络(预览)”。

1. 切换到“专用终结点连接”选项卡。

1. 选择“+ 专用终结点”，打开“创建专用终结点”设置。

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Azure 门户的屏幕截图，其中显示了现有 Azure 数字孪生实例的“网络”页，突出显示了如何创建专用终结点。" lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. 在“基本信息” ****  选项卡中，输入或选择项目的“订阅”和“资源组”，以及终结点的“名称”和“区域”。 区域需要与要使用的 VNet 的所在区域相同。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Azure 门户的屏幕截图，其中显示了“创建专用终结点”对话框的第一个选项卡（“基本信息”选项卡）。它包含上述字段。":::

    完成后，选择“下一步: 资源 >”按钮以转到下一个选项卡。

1. 在“资源”选项卡中，输入或选择以下信息： 
    * **连接方法**：选择“连接到我的目录中的 Azure 资源”，以搜索 Azure 数字孪生实例。
    * **订阅**：输入你的订阅。
    * **资源类型**：选择 "Microsoft.DigitalTwins/digitalTwinsInstances”
    * **资源**：选择 Azure 数字孪生实例的名称。
    * **目标子资源**：选择 "API”。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Azure 门户的屏幕截图，其中显示了“创建专用终结点”对话框的第二个选项卡（“资源”选项卡）。它包含上述字段。":::

    完成后，选择“下一步: 配置 >”按钮以转到下一个选项卡。    

1. 在“配置”选项卡中，输入或选择以下信息 **** ：
    * **虚拟网络**：选择虚拟网络。
    * **子网**：从虚拟网络中选择一个子网。
    * **与专用 DNS 区域集成**：选择是否“与专用 DNS 区域集成”。 可以使用默认值“是”，或者为了便于选择，通过门户中的链接来[详细了解专用 DNS 集成](../private-link/private-endpoint-overview.md#dns-configuration)。
    如果选择“是”，则可以保留默认配置信息。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Azure 门户的屏幕截图，其中显示了“创建专用终结点”对话框的第三个选项卡（“配置”选项卡）。它包含上述字段。":::

    完成后，可以选择“预览 + 创建”按钮以完成设置。 

1. 在“预览 + 创建”选项卡中查看所做的选择，然后选择“创建”按钮。 **** 

终结点部署完成后，它应显示在 Azure 数字孪生实例的专用终结点连接中。

# <a name="cli"></a>[CLI](#tab/cli)

要使用 Azure CLI 创建专用终结点并将其链接到 Azure 数字孪生实例，请使用 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create) 命令。 在 `--private-connection-resource-id` 参数中使用其完全限定的 ID 标识 Azure 数字孪生实例。

下面是一个使用命令创建专用终结点（仅包含所需参数）的示例。

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

有关必需参数和可选参数的完整列表以及更多专用终结点创建示例，请参阅 [az network private-endpoint create 参考文档](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create)。

--- 

## <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

在本部分中，你将了解在创建专用终结点后，如何查看、编辑和删除专用终结点。

# <a name="portal"></a>[Portal](#tab/portal)

为 Azure 数字孪生实例创建专用终结点后，可以在 Azure 数字孪生实例的“网络(预览)”选项卡中查看它。 此页将显示与实例关联的所有专用终结点连接。

:::image type="content" source="media/how-to-enable-private-link/view-endpoint-digital-twins.png" alt-text="Azure 门户的屏幕截图，显示了现有 Azure 数字孪生实例的“网络”页，其中有一个专用终结点。" lightbox="media/how-to-enable-private-link/view-endpoint-digital-twins.png":::


选择终结点可以查看详细信息，更改其配置设置，或删除连接。

>[!TIP]
> 也可以从 Azure 门户的“专用链接中心”查看该终结点。

# <a name="cli"></a>[CLI](#tab/cli)

为 Azure 数字孪生实例创建专用终结点后，可使用 [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) 命令继续管理与实例相关的专用终结点连接。 运算包括：
* 显示专用终结点连接
* 设置专用终结点连接的状态
* 删除专用终结点连接
* 列出实例的所有专用终结点连接

有关详细信息和示例，请参阅 [az dt network private-endpoint 参考文档](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true)。

### <a name="get-additional-private-link-information"></a>获取专用链接的更多信息

你可以通过 [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true) 命令获取有关实例专用链接状态的更多信息。 运算包括：
* 列出与 Azure 数字孪生实例关联的专用链接
* 显示与实例关联的专用链接

有关详细信息和示例，请参阅 [az dt network private-link 参考文档](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true)。

--- 

## <a name="disable--enable-public-network-access-flags"></a>禁用/启用公用网络访问标志

可以将 Azure 数字孪生实例配置为拒绝所有公用连接，并仅允许专用终结点连接，以此增强网络安全性。 使用公用网络访问标志完成此操作。 

此策略支持将 API 访问限制为仅使用专用链接连接。 将公用网络访问标志设置为“已禁用”时，所有从公有云对 Azure 数字孪生实例数据平面的 REST API 调用都将返回 `403, Unauthorized`。 或者，如果将策略设置为“已禁用”，并通过专用终结点发出请求，则 API 调用将成功。

可以使用 [Azure 门户](https://portal.azure.com)、[Azure CLI](/cli/azure/) 或 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)更新网络标志的值。

# <a name="portal"></a>[Portal](#tab/portal-2)

若要在 [Azure 门户](https://portal.azure.com)中禁用或启用公用网络访问，请打开门户并导航到 Azure 数字孪生实例。

1. 在左侧菜单中选择“网络(预览)”。

1. 在“公共访问”选项卡中，将“允许公用网络访问”设置为“已禁用”或“所有网络”   。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure 门户的屏幕截图，其中显示 Azure 数字孪生实例的“网络”页，突出显示了如何切换公共访问。" lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    选择“保存”。

# <a name="cli"></a>[CLI](#tab/cli-2)

在 Azure CLI 中，可以通过将 `--public-network-access` 参数添加到 `az dt create` 命令来禁用或启用公用网络访问。 尽管此命令也可用于创建新的实例，但你可以使用它来编辑现有实例的属性，方法是向其提供已存在的实例的名称。 （有关此命令的详细信息，请参阅其[参考文档](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create)或[设置 Azure 数字孪生实例的常规说明](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)）。

若要对 Azure 数字孪生实例禁用公用网络访问，请使用 `--public-network-access` 参数，如下所示：

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

若要对当前禁用的实例启用公用网络访问，请使用以下类似命令：

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

# <a name="armclient"></a>[ARMClient](#tab/arm-client-2)

使用 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)，可通过以下命令启用或禁用公用网络访问。 

**禁用** 公用网络访问：
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

**启用** 公用网络访问：  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

---


## <a name="next-steps"></a>后续步骤

详细了解 Azure 专用链接： 
* [什么是 Azure 专用链接服务？](../private-link/private-link-service-overview.md)