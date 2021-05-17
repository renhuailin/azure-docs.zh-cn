---
title: 使用专用链接实现专用访问（预览）- CLI
titleSuffix: Azure Digital Twins
description: 请参阅如何在 Azure CLI 中使用专用链接对 Azure 数字孪生解决方案实现专用访问。
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 96a98f4441fca47d53dc9ee54a5586e2b535f96f
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109788384"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>使用专用链接实现专用访问（预览）：Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

本文介绍[为 Azure 数字孪生实例启用包含专用终结点的专用链接](concepts-security.md#private-network-access-with-azure-private-link-preview)的不同方式（当前为预览版）。 通过为 Azure 数字孪生实例配置专用终结点，可以保护 Azure 数字孪生实例，消除曝光危险，以及避免 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 数据外泄。

本文介绍如何使用 [Azure CLI](/cli/azure/what-is-azure-cli)完成此过程。

本文介绍了以下步骤： 
1. 打开专用链接，并为 Azure 数字孪生实例配置专用终结点。
1. 禁用或启用公用网络访问标志，以将 API 访问限制为仅使用专用链接连接。

## <a name="prerequisites"></a>必备条件

在设置专用终结点之前，需要一个可在其中部署终结点的 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 。 如果尚无 VNet，可以按照 Azure 虚拟网络[快速入门](../virtual-network/quick-create-portal.md)中的一种方式进行设置。

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>管理 Azure 数字孪生实例的专用终结点 

使用 [Azure CLI](/cli/azure/what-is-azure-cli) 时，可以使用已存在的 Azure 数字孪生实例上的专用链接设置专用终结点（不能在实例创建过程中添加）。 然后，可以继续通过其他 CLI 命令查看和管理它们。 

>[!TIP]
> 此外，还可以通过专用链接服务设置专用链接终结点，而非通过 Azure 数字孪生实例。 此方法也将提供相同的配置选项和相同的最终结果。
>
> 有关设置专用链接资源的详细信息，请参阅 [Azure 门户](../private-link/create-private-endpoint-portal.md)、[Azure CLI](../private-link/create-private-endpoint-cli.md)、[ARM 模板](../private-link/create-private-endpoint-template.md)或 [PowerShell](../private-link/create-private-endpoint-powershell.md) 的专用链接文档。

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>将专用终结点添加到现有实例

要创建专用终结点并将其链接到 Azure 数字孪生实例，请使用 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 命令。 在 `--private-connection-resource-id` 参数中使用其完全限定的 ID 标识 Azure 数字孪生实例。

下面是一个使用命令创建专用终结点（仅包含所需参数）的示例。

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} --name {name_for_private_endpoint} --resource-group {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

有关必需参数和可选参数的完整列表以及更多专用终结点创建示例，请参阅 [az network private-endpoint create 参考文档](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)。

### <a name="manage-private-endpoint-connections-on-the-instance"></a>管理实例上的专用终结点连接

为 Azure 数字孪生实例创建专用终结点后，可使用 [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection) 命令继续管理与实例相关的专用终结点连接。 运算包括：
* 显示专用终结点连接
* 设置专用终结点连接的状态
* 删除专用终结点连接
* 列出实例的所有专用终结点连接

有关详细信息和示例，请参阅 [az dt network private-endpoint 参考文档](/cli/azure/dt/network/private-endpoint)。

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>管理 Azure 数字孪生实例上的其他专用链接信息

你可以通过 [az dt network private-link](/cli/azure/dt/network/private-link) 命令获取有关实例专用链接状态的更多信息。 运算包括：
* 列出与 Azure 数字孪生实例关联的专用链接
* 显示与实例关联的专用链接

有关详细信息和示例，请参阅 [az dt network private-link 参考文档](/cli/azure/dt/network/private-link)。

## <a name="disable--enable-public-network-access-flags"></a>禁用/启用公用网络访问标志

可以将 Azure 数字孪生实例配置为拒绝所有公用连接，并仅允许专用终结点连接，以此增强网络安全性。 使用公用网络访问标志完成此操作。 

此策略支持将 API 访问限制为仅使用专用链接连接。 将公用网络访问标志设置为“已禁用”时，所有从公有云对 Azure 数字孪生实例数据平面的 REST API 调用都将返回 `403, Unauthorized`。 或者，如果将策略设置为“已禁用”，并通过专用终结点发出请求，则 API 调用将成功。

本文介绍如何使用 [Azure CLI](/cli/azure/) 或 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)更新网络标志的值。 有关如何利用 Azure 门户执行此操作的说明，请参阅本文的[门户版本](how-to-enable-private-link-portal.md)。

### <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中，可以通过将 `--public-network-access` 参数添加到 `az dt create` 命令来禁用或启用公用网络访问。 尽管此命令也可用于创建新的实例，但你可以使用它来编辑现有实例的属性，方法是向其提供已存在的实例的名称。 （有关此命令的详细信息，请参阅其[参考文档](/cli/azure/dt#az_dt_create)或[设置 Azure 数字孪生实例的常规说明](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)）。

若要对 Azure 数字孪生实例禁用公用网络访问，请使用 `--public-network-access` 参数，如下所示：

```azurecli-interactive
az dt create --dt-name {name_of_existing_instance} --resource-group {resource_group} --public-network-access Disabled
```

若要对当前禁用的实例启用公用网络访问，请使用以下类似命令：

```azurecli-interactive
az dt create --dt-name {name_of_existing_instance} --resource-group {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>使用 ARMClient 命令工具 

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

## <a name="next-steps"></a>后续步骤

详细了解 Azure 专用链接： 
* [什么是 Azure 专用链接服务？](../private-link/private-link-service-overview.md)