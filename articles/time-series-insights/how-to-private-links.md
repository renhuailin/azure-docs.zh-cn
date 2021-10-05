---
title: 启用专用链接 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解如何使用 Azure 门户通过专用链接为 Azure 时序见解第 2 代解决方案实现专用访问。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 21a6c529aaa086fa75aef9e665cbbdce882baf5a
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061906"
---
# <a name="enable-private-access-for-tsi-with-private-link-preview"></a>使用专用链接为 TSI 实现专用访问（预览版）

本文介绍如何[为 Azure 时序见解第 2 代环境启用具有专用终结点的专用链接](concepts-private-links.md)（当前为预览版）。 通过为 Azure 时序见解第 2 代环境配置专用终结点，可以保护 Azure 时序见解环境，消除公开暴露，以及避免 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 数据外泄。

本文将逐步介绍如何使用 [**Azure 门户**](https://portal.azure.com)完成此过程。

本文介绍了以下步骤： 
1. 启用专用链接，并为时序见解第 2 代环境配置专用终结点。
1. 禁用或启用公用网络访问标志，以将访问限制为仅使用专用链接连接。

## <a name="prerequisites"></a>先决条件

在设置专用终结点之前，需要一个可在其中部署终结点的 [**Azure 虚拟网络 (VNet)**](../virtual-network/virtual-networks-overview.md)。 如果尚无 VNet，可以按照 Azure 虚拟网络[快速入门](../virtual-network/quick-create-portal.md)中的一种方式进行设置。

## <a name="add-a-private-endpoint-for-an-azure-time-series-insights-gen2-environment"></a>为 Azure 时序见解第 2 代环境添加专用终结点 

使用 [Azure 门户](https://portal.azure.com)时，可以选择为 Azure 时序见解第 2 代环境启用具有专用终结点的专用链接作为环境初始设置的一部分，也可以稍后在已存在的环境上启用它。 

这些创建方法都将为环境提供相同的配置选项和相同的最终结果。 本节将介绍如何实现这些方法。

>[!TIP]
> 此外，还可以通过专用链接服务而不是通过 Azure 时序见解第 2 代环境设置专用链接终结点。 此方法也将提供相同的配置选项和相同的最终结果。
>
> 有关设置专用链接资源的详细信息，请参阅 [Azure 门户](../private-link/create-private-endpoint-portal.md)、[Azure CLI](../private-link/create-private-endpoint-cli.md)、[ARM](../private-link/create-private-endpoint-template.md) 或 [PowerShell](../private-link/create-private-endpoint-powershell.md) 的专用链接文档。

### <a name="add-a-private-endpoint-during-environment-creation"></a>在环境创建过程中添加专用终结点

在本部分，你将在当前正在创建的 Azure 时序见解第 2 代环境上启用具有专用终结点的专用链接。 本部分重点介绍创建过程的网络步骤；有关创建新的 Azure 时序见解第 2 代环境的完整演练，请参阅[操作说明：设置环境](tutorial-set-up-environment.md)。

“专用链接”选项位于环境设置的“网络”选项卡中。

在此选项卡中，可以通过在“连接方法”中选择“专用终结点”选项来启用专用终结点。

此操作将会添加一个名为“专用终结点连接”的部分，在其中可以配置专用终结点的详细信息。 选择“+ 添加”按钮继续操作。

:::image type="content" source="media/private-links/create-instance-networking.png" alt-text="Azure 门户的屏幕截图，其中显示了时序见解第 2 代的“创建资源”对话框的“网络”选项卡。突出显示了选项卡名称、连接方法的“专用终结点”选项以及用于新建专用终结点连接的“+ 添加”按钮。" lightbox="media/private-links/create-instance-networking.png":::

将打开一个页面，用于输入新专用终结点的详细信息。

:::image type="content" source="media/private-links/create-private-endpoint.png" alt-text="Azure 门户的屏幕截图，其中显示“创建专用终结点”页。它包含下面所述的字段。":::

1. 为“订阅”和“资源组”填充所选内容。 将“位置”设置为与要使用的 VNet 相同的位置。 选择终结点名称，并为“目标子资源”选择“environment”或“tsiExplorer” 。

1. 接下来，选择要用于部署终结点的“虚拟网络”和“子网”。

1. 最后，选择是否 **与专用 DNS 区域集成**。 可以使用默认值“是”，或者为了便于选择，通过门户中的链接来[详细了解专用 DNS 集成](../private-link/private-endpoint-overview.md#dns-configuration)。

填写完配置选项后，单击 **确定** 完成此操作。

你将返回到 Azure 时序见解第 2 代环境设置的“网络”选项卡，在其中的“专用终结点连接”下应可以看到新的终结点 。

然后，可以使用底部的导航按钮继续完成余下的环境设置。

### <a name="add-a-private-endpoint-to-an-existing-environment"></a>将专用终结点添加到现有环境

在本部分，你将为已存在的 Azure 时序见解第 2 代环境启用具有专用终结点的专用链接。

1. 首先，在浏览器中导航到 [Azure 门户](https://portal.azure.com)。 通过在门户搜索栏中搜索 Azure 时序见解第 2 代环境的名称来找出该环境。

1. 在左侧菜单中选择“网络(预览)”。

1. 切换到“专用终结点连接”选项卡。

1. 选择“+ 专用终结点”，打开“创建专用终结点”设置。

    :::image type="content" source="media/private-links/add-private-endpoint.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure 时序见解第 2 代环境的“网络(预览版)”页。突出显示了“专用终结点连接”选项卡和“+ 专用终结点”按钮。" lightbox="media/private-links/add-private-endpoint.png":::

1. 在“基本信息” ****  选项卡中，输入或选择项目的“订阅”和“资源组”，以及终结点的“名称”和“区域”。 区域需要与要使用的 VNet 的所在区域相同。

    :::image type="content" source="media/private-links/create-private-endpoint-2.png" alt-text="Azure 门户的屏幕截图，其中显示了“创建专用终结点”对话框的第一个选项卡（“基本信息”选项卡）。它包含上述字段。":::

    完成后，选择“下一步: 资源 >”按钮以转到下一个选项卡。

1. 在“资源”选项卡中，输入或选择以下信息： 
    * 连接方法：选择“连接到我的目录中的 Azure 资源”，以搜索你的 Azure 时序见解第 2 代环境 。
    * **订阅**：输入你的订阅。
    * 资源类型：选择“Microsoft.TimeSeriesInsights/environments” 
    * 资源：选择你的 Azure 时序见解第 2 代环境的名称。
    * 目标子资源：选择“environment”或“tsiExplorer”  。

    :::image type="content" source="media/private-links/create-private-endpoint-3.png" alt-text="Azure 门户的屏幕截图，其中显示了“创建专用终结点”对话框的第二个选项卡（“资源”选项卡）。它包含上述字段。":::

    完成后，选择“下一步: 配置 >”按钮以转到下一个选项卡。    

1. 在“配置”选项卡中，输入或选择以下信息 **** ：
    * **虚拟网络**：选择虚拟网络。
    * **子网**：从虚拟网络中选择一个子网。
    * **与专用 DNS 区域集成**：选择是否“与专用 DNS 区域集成”。 可以使用默认值“是”，或者为了便于选择，通过门户中的链接来[详细了解专用 DNS 集成](../private-link/private-endpoint-overview.md#dns-configuration)。
    如果选择“是”，则可以保留默认配置信息。

    :::image type="content" source="media/private-links/create-private-endpoint-4.png" alt-text="Azure 门户的屏幕截图，其中显示了“创建专用终结点”对话框的第三个选项卡（“配置”选项卡）。它包含上述字段。":::

    完成后，可以选择“预览 + 创建”按钮以完成设置。 

1. 在“预览 + 创建”选项卡中查看所做的选择，然后选择“创建”按钮。 **** 

终结点部署完成后，它应显示在 Azure 时序见解第 2 代环境的专用终结点连接中。

>[!TIP]
> 也可以从 Azure 门户的“专用链接中心”查看该终结点。

## <a name="disable--enable-public-network-access-flags"></a>禁用/启用公用网络访问标志

可以将 Azure 时序见解第 2 代环境配置为拒绝所有公用连接，并仅允许专用终结点连接，以此增强网络安全性。 使用公用网络访问标志完成此操作。 

使用此策略可将访问限制为仅使用专用链接连接。 将公用网络访问标志设置为“已禁用”时，所有从公有云对 Azure 时序见解第 2 代环境数据平面进行的 REST API 调用都将返回 `403, Unauthorized`。 或者，如果将策略设置为“已禁用”，并通过专用终结点发出请求，则 API 调用将成功。

若要在 [Azure 门户](https://portal.azure.com)中禁用或启用公用网络访问，请打开门户并导航到你的 Azure 时序见解第 2 代环境。

1. 在左侧菜单中选择“网络(预览)”。

1. 在“公共访问”选项卡中，将“允许公用网络访问”设置为“已禁用”或“所有网络”   。

    :::row:::
        :::column:::
            :::image type="content" source="media/private-links/network-flag-portal.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure 时序见解第 2 代环境的“网络(预览版)”页。突出显示了“公共访问”选项卡以及用于选择是否允许公用网络访问的选项。" lightbox="media/private-links/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    选择“保存”。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 专用链接： 
* [*什么是 Azure 专用链接服务？*](../private-link/private-link-service-overview.md)