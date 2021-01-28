---
title: '使用专用链接 (预览启用专用访问) '
titleSuffix: Azure Digital Twins
description: 请参阅如何为具有专用链接的 Azure 数字孪生解决方案启用专用访问
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a5328f52975e72298b93107792692b178dac8a97
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948704"
---
# <a name="enable-private-access-with-private-link-preview"></a>使用专用链接 (预览启用专用访问) 

本文介绍了 [使用 Azure 数字孪生实例的专用终结点实现专用链接](concepts-security.md#private-network-access-with-azure-private-link-preview) 的不同方式， (当前在预览版中) 。 为 Azure 数字孪生实例配置专用终结点可让你保护 Azure 数字孪生实例，消除公开公开，同时避免 Azure 虚拟网络中的数据渗透 [ (VNet) ](../virtual-network/virtual-networks-overview.md)。

下面是本文中所述的步骤： 
1. 打开专用链接，并为 Azure 数字孪生实例配置专用终结点。
1. 禁用或启用公用网络访问标志，以仅限制对专用链接连接的 API 访问。

## <a name="prerequisites"></a>必备条件

在设置专用终结点之前，需要一个 [**Azure 虚拟网络 (VNet)**](../virtual-network/virtual-networks-overview.md) 可在其中部署终结点。 如果尚未安装 VNet，可以按照 Azure 虚拟网络 [快速入门](../virtual-network/quick-create-portal.md) 中的一项进行设置。

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>为 Azure 数字孪生实例添加专用终结点 

在实例的初始设置过程中，你可以使用 Azure 数字孪生实例的专用终结点打开专用链接，或稍后在已存在的实例上启用它。 

这些创建方法中的任何一种都将为你的实例提供相同的配置选项和相同的最终结果。 本部分介绍如何在 [Azure 门户](https://portal.azure.com)中执行每一个操作。

>[!TIP]
> 还可以通过专用链接服务而不是通过 Azure 数字孪生实例来设置专用链接终结点。 这还提供相同的配置选项和相同的最终结果。
>
> 有关设置专用链接资源的详细信息，请参阅 [Azure 门户](../private-link/create-private-endpoint-portal.md)、 [Azure CLI](../private-link/create-private-endpoint-cli.md)、 [ARM](../private-link/create-private-endpoint-template.md)或 [PowerShell](../private-link/create-private-endpoint-powershell.md)的专用链接文档。

### <a name="add-a-private-endpoint-during-instance-creation"></a>在实例创建过程中添加专用终结点

在本部分中，将使用 [Azure 门户](https://portal.azure.com) 在当前正在创建的 Azure 数字孪生实例上启用私有终结点链接。 本部分重点介绍创建过程的网络步骤;有关创建新的 Azure 数字孪生实例的完整演练，请参阅 [*如何：设置实例和身份验证*](how-to-set-up-instance-portal.md)。

专用链接选项位于实例设置的 " **网络** " 选项卡中。

在此选项卡中，可以通过选择 **连接方法** 的 "**专用终结点**" 选项来启用私有终结点。

这会添加一个名为 " **专用终结点连接** " 的部分，你可以在其中配置专用终结点的详细信息。 选择 " **+ 添加** " 按钮继续。

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure 门户显示 Azure 数字孪生的 &quot;创建资源&quot; 对话框的 &quot;网络&quot; 选项卡的屏幕截图。选项卡名称周围有一个突出显示，连接方法的专用终结点选项，另外还提供了 &quot;+ 添加&quot; 按钮以创建新的专用终结点连接。" lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

这会打开一个页面，用于输入新专用终结点的详细信息。

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="显示 &quot;创建专用终结点&quot; 页的 Azure 门户屏幕截图。它包含如下所述的字段。":::

1. 为你的 **订阅** 和 **资源组** 填写选择。 将 **位置** 设置为与将使用的 VNet 相同的位置。 选择终结点的 **名称** ，并选择 " **目标子资源** " "选择 *API*"。

1. 接下来，选择要用于部署终结点的 **虚拟网络** 和 **子网** 。

1. 最后，选择是否要 **与专用 DNS 区域集成**。 你可以使用默认值 **"是"** 或，对于此选项，你可以按照门户中的链接来 [了解有关专用 DNS 集成的详细信息](../private-link/private-endpoint-overview.md#dns-configuration)。

填写配置选项后，请单击 **"确定"** 完成操作。

这会返回到 Azure 数字孪生实例设置的 " **网络** " 选项卡，在该选项卡中，新终结点应在 "专用终结点连接" 下可见。

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure 门户显示 Azure 数字孪生的 &quot;创建资源&quot; 对话框的 &quot;网络&quot; 选项卡的屏幕截图。新的专用终结点连接有一个突出显示，导航按钮 (查看 + 创建、上一个、下一步：高级) 。" lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

然后，你可以使用底部导航按钮继续实例安装程序的其余部分。

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>将私有终结点添加到现有实例

在本部分中，将使用 [Azure 门户](https://portal.azure.com) 对已存在的 Azure 数字孪生实例启用专用终结点链接。

1. 首先，在浏览器中导航到 [Azure 门户](https://portal.azure.com) 。 通过在门户搜索栏中搜索 Azure 数字孪生实例的名称来将其打开。

1. 选择左侧菜单中的 " **网络 (预览")** 。

1. 切换到 **专用终结点连接** 选项卡。

1. 选择 " **+ 专用终结点** " 以打开 " **创建专用终结点** " 设置。

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="显示 Azure 数字孪生实例的网络 (预览) 页面的 Azure 门户屏幕截图。&quot;专用终结点连接&quot; 选项卡将突出显示，并且还会突出显示 &quot;+ 专用终结点&quot; 按钮。" lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. 在 " **基本信息**   " 选项卡中，输入或选择项目的 **订阅** 和 **资源组**，以及终结点的 **名称** 和 **区域**。 区域需要与所使用的 VNet 的区域相同。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="显示 &quot;创建专用终结点&quot; 对话框的第一个 (基础) 选项卡的 Azure 门户屏幕截图。它包含上述字段。":::

    完成后，选择 " **下一步：资源 >** " 按钮以切换到下一个选项卡。

1. 在 " **资源** " 选项卡中，输入或选择以下信息： 
    * **连接方法**： **在 "目录" 中选择 "连接到 azure 资源"** ，搜索 azure 数字孪生实例。
    * **订阅**：输入订阅。
    * **资源类型**：选择 " **DigitalTwins"/"digitalTwinsInstances** "
    * **资源**：选择 Azure 数字孪生实例的名称。
    * **目标子资源**：选择 " **API**"。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="显示 &quot;创建专用终结点&quot; 对话框的第二个 &quot; (资源) &quot; 选项卡的 Azure 门户屏幕截图。它包含上述字段。":::

    完成后，选择 " **下一步：配置 >** " 按钮以切换到下一个选项卡。    

1. 在 " **配置** " 选项卡中，输入或选择以下信息：
    * **虚拟网络**：选择虚拟网络。
    * **子网**：从虚拟网络中选择一个子网。
    * **与专用 dns 区域集成**：选择是否 **与专用 dns 区域集成**。 你可以使用默认值 **"是"** 或，对于此选项，你可以按照门户中的链接来 [了解有关专用 DNS 集成的详细信息](../private-link/private-endpoint-overview.md#dns-configuration)。
    如果选择 **"是"**，则可以保留默认配置信息。

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="显示 &quot;创建专用终结点&quot; 对话框的第三个 (配置) &quot;选项卡的 Azure 门户屏幕截图。它包含上述字段。":::

    完成后，可以选择 "查看" 和 " **创建** " 按钮以完成设置。 

1. 在 " **查看** " 和 "创建" 选项卡中，查看你的选择，然后选择 " **创建** " 按钮。 

终结点部署完成后，它应显示在 Azure 数字孪生实例的专用终结点连接中。

>[!TIP]
> 也可以从 Azure 门户中的专用链接中心查看终结点。

## <a name="disable--enable-public-network-access-flags"></a>禁用/启用公用网络访问标志

可以将 Azure 数字孪生实例配置为拒绝所有公用连接，并只允许通过专用终结点的连接来增强网络安全。 此操作通过 **公共网络访问标志** 来完成。 

此策略允许你限制仅限对专用链接连接进行 API 访问。 当 "公用网络访问" 标志设置为 " *已禁用*" 时，所有从公有云对 Azure 数字孪生实例数据平面的 REST API 调用都将返回 `403, Unauthorized` 。 或者，如果将策略设置为 " *已禁用* "，并且通过专用终结点发出请求，则 API 调用将成功。

您可以使用 [Azure 门户](https://portal.azure.com)、 [Azure CLI](/cli/azure/)或 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)更新网络标志的值。

### <a name="option-1-using-the-azure-portal"></a>选项1：使用 Azure 门户

若要在 [Azure 门户](https://portal.azure.com)中禁用或启用公用网络访问，请打开门户并导航到 Azure 数字孪生实例。

1. 选择左侧菜单中的 " **网络 (预览")** 。

1. 在 " **公共访问** " 选项卡中，将 " **允许公共网络访问** " 设置为 " **禁用** " 或 " **所有网络**"。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Azure 门户显示 Azure 数字孪生实例的网络 (预览) 页的屏幕截图。&quot;公共访问&quot; 选项卡将突出显示，并且还将突出显示用于选择是否允许公共网络访问的选项。" lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    选择“保存”。

### <a name="option-2-using-the-azure-cli"></a>选项2：使用 Azure CLI

使用 Azure CLI，可以使用命令完成此操作，如下 `az resource update` 所示。 可以将 Azure 数字孪生实例上的 publicNetworkAccess 属性设置为 `disabled` 或 `enabled` 。

若要禁用标志，请使用以下命令，并将占位符替换为你的实例值。
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=disabled  
```

下面是输出的外观的屏幕截图。

:::image type="content" source="media/how-to-enable-private-link/network-flag-cli.png" alt-text="运行上述 Azure CLI 命令的终端窗口的屏幕截图。输出包含实例的详细信息，其中包括一个名为 publicNetworkAccess 的属性，其值为 Disabled。" lightbox="media/how-to-enable-private-link/network-flag-cli.png":::

若要启用标志，请使用下面的类似命令。
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=enabled  
```

### <a name="option-3-usingarmclient"></a>选项3：使用 ARMClient  

使用 [ARMClient 命令工具](https://github.com/projectkudu/ARMClient)，可使用以下命令启用或禁用公共网络访问。 

若要禁用公共网络访问：
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

若要启用公共网络访问：  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>后续步骤

详细了解 Azure 的专用链接： 
* [*什么是 Azure 专用链接服务？*](../private-link/private-link-service-overview.md)