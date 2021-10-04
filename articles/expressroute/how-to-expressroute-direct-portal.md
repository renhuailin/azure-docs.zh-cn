---
title: Azure ExpressRoute：配置 ExpressRoute Direct：门户
description: 本页面帮助你使用门户配置 ExpressRoute Direct。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/05/2021
ms.author: duau
ms.openlocfilehash: d5b7bba4774eb81c684875a4db9ffb1afb2a60aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811239"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>使用 Azure 门户创建 ExpressRoute Direct

本文介绍了如何使用 Azure 门户创建 ExpressRoute Direct。
使用 ExpressRoute Direct，可以直接连接到 Microsoft 战略性分布在全球的对等互连位置的的全球网络。 有关详细信息，请参阅[关于 ExpressRoute Direct](expressroute-erdirect-about.md)。

## <a name="before-you-begin"></a><a name="before"></a>准备工作

需要先注册订阅，然后才能使用 ExpressRoute Direct。 若要注册，请通过 Azure PowerShell 执行以下操作：
1.  登录到 Azure 并选择想要注册的订阅。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 使用以下命令注册公共预览版订阅：
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

注册后，验证 Microsoft.Network 资源提供程序是否已在你的订阅中注册。 通过注册资源提供程序来配置订阅，以供资源提供程序使用。

1. 按照 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)中所述访问你的订阅设置。
1. 在你的订阅中，对于“资源提供程序”，请验证 **Microsoft.Network** 提供程序是否显示为“已注册”状态。 如果已注册的提供程序的列表中不存在 Microsoft.Network 资源提供程序，请添加该提供程序。

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>创建 ExpressRoute Direct

1. 在 [Azure 门户](https://portal.azure.com)菜单或“主页”中，选择“创建资源” 。

1. 在“新建”页面上的“搜索市场”字段中键入“ExpressRoute Direct”，然后选择 Enter 转到搜索结果  。

1. 从结果中选择“ExpressRoute Direct”。

1. 在“ExpressRoute Direct”页面上，选择“创建”以打开“创建 ExpressRoute Direct”页面。

1. 首先，请填写“基本信息”页面上的字段。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="“基本信息”页":::

    * **订阅**：你要用来新建 ExpressRoute Direct 的 Azure 订阅。 ExpressRoute Direct 资源和 ExpressRoute 线路必须位于同一订阅中。
    * **资源组**：将在其中新建 ExpressRoute Direct 资源的 Azure 资源组。 如果目前没有资源组，可新建一个。
    * **区域**：将在其中创建资源的 Azure 公共区域。
    * **名称**：新的 ExpressRoute Direct 资源的名称。

1. 接下来，完成“配置”页上的字段。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="屏幕截图显示了“创建 ExpressRoute Direct”页面，其中选择了“配置”选项卡。":::

    * **对等互连位置**：将连接到 ExpressRoute Direct 资源的对等互连位置。 有关对等互连位置的详细信息，请查看 [ExpressRoute 位置](expressroute-locations-providers.md)。
   * 带宽：要保留的端口对带宽。 ExpressRoute Direct 同时支持 10 Gb 和 100 Gb 带宽选项。 如果所需的带宽在指定的对等互连位置不可用，请[在 Azure 门户中提交支持请求](https://aka.ms/azsupt)。
   * **封装**：ExpressRoute Direct 同时支持 QinQ 和 Dot1Q 封装。
     * 如果选择了 QinQ，则会动态为每个 ExpressRoute 线路分配一个 S-Tag，并且每个线路在整个 ExpressRoute Direct 资源中将是唯一的。
     *  线路上的每个 C-Tag 在该线路上必须是唯一的，但在整个 ExpressRoute Direct 中不必唯一。
     * 如果选择了 Dot1Q 封装，则必须在整个 ExpressRoute Direct 资源中管理 C-Tag (VLAN) 的唯一性。
     >[!IMPORTANT]
     >ExpressRoute Direct 只能采用一种封装类型。 无法在创建 ExpressRoute Direct 后更改封装。
     >

1. 指定任何资源标记，然后选择“查看 + 创建”来验证 ExpressRoute Direct 资源设置。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="屏幕截图显示了“创建 ExpressRoute”页面，其中选择了“查看 + 创建”选项卡。":::

1. 选择“创建”  。 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>生成授权信 (LOA)

1. 转至 ExpressRoute Direct 资源的概述页面，选择“生成授权信”。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/overview.png" alt-text="概述页面上的“生成授权信”按钮的屏幕截图。":::

1. 输入公司名称，然后选择“下载”以生成信件。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/letter-of-authorization-page.png" alt-text="授权信页面的屏幕截图。":::

## <a name="change-admin-state-of-links"></a><a name="state"></a>更改链路的管理状态

应当使用此过程执行第 1 层测试，以确保每个交叉连接都已针对主端口和辅助端口正确设置到每台路由器中。

1. 在 ExpressRoute Direct 资源“概述”页面的“链接”部分中，选择“link1”。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="链接 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. 将“管理员状态”设置切换为“已启用”，然后选择“保存”。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="管理员状态":::

    >[!IMPORTANT]
    >在任一链接上启用管理员状态后，将开始计费。
    >

1. 对“link2”重复相同的过程。

## <a name="create-a-circuit"></a><a name="circuit"></a>创建线路

默认情况下，可以在 ExpressRoute Direct 资源所在的订阅中创建 10 条线路。 可以联系支持人员来提高此数目。 你负责跟踪预配的和已利用的带宽。 预配带宽是 ExpressRoute Direct 资源上所有线路的带宽总和。 利用带宽是基础物理接口的物理用法。

* 有额外的线路带宽可以在 ExpressRoute Direct 上使用，仅用于支持上面概述的场景。 其中包括：40 Gbps 和 100 Gbps。

* SkuTier 可以为“本地”、“标准”或“高级”。

* SkuFamily 只能为 MeteredData。 ExpressRoute Direct 不支持“无限制”。

以下步骤可帮助你通过 ExpressRoute Direct 工作流创建 ExpressRoute 线路。 如果你愿意，也可以使用常规线路工作流来创建线路，但是，对于此配置，使用常规线路工作流步骤没有优势。 请参阅[创建和修改 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)。

1. 在 ExpressRoute Direct 的“设置”部分中，选择“线路”，然后选择“+添加”。 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="屏幕截图显示了 ExpressRoute 设置，其中选择了“线路”并突出显示了“添加”。" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. 在“配置”页面中配置设置。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="“配置”页面 - ExpressRoute Direct":::

1. 指定任何资源标记，然后选择“查看 + 创建”，以在创建资源之前验证这些值。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="“查看 + 创建”- ExpressRoute Direct":::

1. 选择“创建”  。 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 

## <a name="next-steps"></a>后续步骤

创建 ExpressRoute 线路后，可以[将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-add-gateway-portal-resource-manager.md)。
