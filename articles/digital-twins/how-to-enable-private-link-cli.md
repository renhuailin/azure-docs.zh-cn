---
title: 使用专用链接 (预览启用专用访问) -CLI
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure CLI 为具有专用链接的 Azure 数字孪生解决方案启用私有访问权限。
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: cbaa83b38482203655f7de98cd5bbfec3ef7a870
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417141"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>使用专用链接 (预览) 启用专用访问： Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

本文介绍了 [使用 Azure 数字孪生实例的专用终结点实现专用链接](concepts-security.md#private-network-access-with-azure-private-link-preview) 的不同方式， (当前在预览版中) 。 为 Azure 数字孪生实例配置专用终结点可让你保护 Azure 数字孪生实例，消除公开公开，同时避免 Azure 虚拟网络中的数据渗透 [ (VNet) ](../virtual-network/virtual-networks-overview.md)。

本文逐步介绍了如何使用 [**Azure CLI**](/cli/azure/what-is-azure-cli)。

下面是本文中所述的步骤： 
1. 打开专用链接，并为 Azure 数字孪生实例配置专用终结点。
1. 禁用或启用公用网络访问标志，以仅限制对专用链接连接的 API 访问。

## <a name="prerequisites"></a>先决条件

在设置专用终结点之前，需要一个 [**Azure 虚拟网络 (VNet)**](../virtual-network/virtual-networks-overview.md) 可在其中部署终结点。 如果尚未安装 VNet，可以按照 Azure 虚拟网络 [快速入门](../virtual-network/quick-create-portal.md) 中的一项进行设置。

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>管理 Azure 数字孪生实例的专用终结点 

使用 [Azure CLI](/cli/azure/what-is-azure-cli)时，可以使用已存在的 Azure 数字孪生实例上的专用链接设置专用终结点， (不能在实例创建) 中添加该实例。 然后，你可以继续通过其他 CLI 命令查看和管理它们。 

>[!TIP]
> 还可以通过专用链接服务而不是通过 Azure 数字孪生实例来设置专用链接终结点。 这还提供相同的配置选项和相同的最终结果。
>
> 有关设置专用链接资源的详细信息，请参阅 [Azure 门户](../private-link/create-private-endpoint-portal.md)、 [Azure CLI](../private-link/create-private-endpoint-cli.md)、 [ARM 模板](../private-link/create-private-endpoint-template.md)或 [PowerShell](../private-link/create-private-endpoint-powershell.md)的专用链接文档。

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>将私有终结点添加到现有实例

若要创建专用终结点并将其链接到 Azure 数字孪生实例，请使用 [**az network private-endpoint create**](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create) 命令。 在参数中使用其完全限定的 ID 标识 Azure 数字孪生实例 `--private-connection-resource-id` 。

下面的示例使用命令创建专用终结点，仅包含所需的参数。

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

有关必需参数和可选参数的完整列表以及更多专用终结点创建示例，请参阅 [ **az 网络专用终结点创建** 参考文档](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create)。

### <a name="manage-private-endpoint-connections-on-the-instance"></a>管理实例上的专用终结点连接

为你的 Azure 数字孪生实例创建专用终结点后，你可以使用 [**az dt 网络专用终结点连接**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) 命令来继续管理与实例相关的专用终结点 **连接** 。 运算包括：
* 显示专用终结点连接
* 设置专用终结点连接的状态
* 删除私有终结点连接
* 列出实例的所有私有终结点连接

有关详细信息和示例，请参阅 [ **az dt 网络专用终结点** 参考文档](/cli/azure/ext/azure-iot/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true)。

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>管理 Azure 数字孪生实例上的其他专用链接信息

你可以通过 [**az dt network Private link**](/cli/azure/ext/azure-iot/dt/network/private-link?view=azure-cli-latest&preserve-view=true) 命令获取有关你的实例的私有链接状态的更多信息。 运算包括：
* 列出与 Azure 数字孪生实例关联的专用链接
* 显示与实例关联的专用链接

有关详细信息和示例，请参阅 [ **az dt 网络专用链接** 参考文档](/cli/azure/ext/azure-iot/dt/network/private-link?view=azure-cli-latest&preserve-view=true)。

## <a name="disable--enable-public-network-access-flags"></a>禁用/启用公用网络访问标志

可以将 Azure 数字孪生实例配置为拒绝所有公用连接，并只允许通过专用终结点的连接来增强网络安全。 此操作通过 **公共网络访问标志** 来完成。 

此策略允许你限制仅限对专用链接连接进行 API 访问。 当 "公用网络访问" 标志设置为 " *已禁用*" 时，所有从公有云对 Azure 数字孪生实例数据平面的 REST API 调用都将返回 `403, Unauthorized` 。 或者，如果将策略设置为 " *已禁用* "，并且通过专用终结点发出请求，则 API 调用将成功。

本文介绍如何使用 [Azure CLI](/cli/azure/) 或 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)更新网络标志的值。 有关如何利用 Azure 门户执行此操作的说明，请参阅本文的 [门户版本](how-to-enable-private-link-portal.md) 。

### <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中，可以通过将参数添加到命令来禁用或启用公用网络访问 `--public-network-access` `az dt create` 。 尽管此命令也可用于创建新的实例，但你可以使用它来编辑现有实例的属性，方法是向其提供已存在的实例的名称。  (有关此命令的详细信息，请参阅本 [文档的参考文档](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_create) 或 [设置 Azure 数字孪生实例的常规说明](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)) 。

若要对 Azure 数字孪生实例 **禁用** 公共网络访问，请使用 `--public-network-access` 如下所示的参数：

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

若要对当前禁用的实例 **启用** 公共网络访问，请使用以下类似命令：

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>使用 ARMClient 命令工具 

使用 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)，可使用以下命令启用或禁用公共网络访问。 

若要 **禁用** 公共网络访问：
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

若要 **启用** 公共网络访问：  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>后续步骤

详细了解 Azure 的专用链接： 
* [*什么是 Azure 专用链接服务？*](../private-link/private-link-service-overview.md)