---
title: NAT 网关集成 - Azure 应用服务 | Microsoft Docs
description: 介绍 NAT 网关如何与 Azure 应用服务集成。
services: app-service
author: madsd
ms.assetid: 0a84734e-b5c1-4264-8d1f-77e781b28426
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 851e1a04b7fa5ac14848c9d679118a8ebf4ada77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778556"
---
# <a name="virtual-network-nat-gateway-integration"></a>虚拟网络 NAT 网关集成

NAT 网关是一项完全托管的、高度可复原的服务，它可以与一个或多个子网关联，并确保所有面向 Internet 的出站流量都将通过该网关路由。 使用应用服务时，有两个可以使用 NAT 网关的重要场景。 

NAT 网关提供了用于面向 Internet 的出站流量的可预测的静态公共 IP。 在有大量到同一公共地址/端口组合的并发连接的场景中，该网关还会显著增加可用的 [SNAT 端口](../troubleshoot-intermittent-outbound-connection-errors.md)。

有关详细信息和定价。 请转到 [NAT 网关概述](../../virtual-network/nat-gateway/nat-overview.md)。

:::image type="content" source="./media/nat-gateway-integration/nat-gateway-overview.png" alt-text="图中显示了流向 Azure 虚拟网络中 NAT 网关的 Internet 流量。":::

> [!Note] 
> 将 NAT 网关与应用服务配合使用取决于区域 VNet 集成，因此，需要“标准”、“高级”、“PremiumV2”或“PremiumV3”应用服务计划   。

## <a name="configuring-nat-gateway-integration"></a>配置 NAT 网关集成

若要配置 NAT 网关与应用服务的集成，需要完成以下步骤：

* 按照[将应用与 Azure 虚拟网络集成](../web-sites-integrate-with-vnet.md)中所述，配置区域 VNet 与应用的集成
* 确保已为 VNet 集成启用了[全部路由](../web-sites-integrate-with-vnet.md#routes)，这样，VNet 中的路由将会影响绑定 Internet 的流量。
* 为 NAT 网关预配公共 IP，并将该 IP 与 VNet 集成子网关联。

通过门户设置 NAT 网关：

1. 在应用服务门户中转到“网络”UI，然后在“出站流量”部分中选择“VNet 集成”。 确保应用程序与某个子网集成，并且已启用“全部路由”。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-route-all-enabled.png" alt-text="已为 VNet 集成启用了“全部路由”的屏幕截图。":::
1. 在 Azure 门户菜单或“主页”页上，选择“创建资源” 。 此时会显示“新建”窗口。
1. 搜索“NAT 网关”并从结果列表中选择它。
1. 填写“基本信息”，并选择应用所在的区域。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-basics.png" alt-text="“创建 NAT 网关”中“基本信息”选项卡的屏幕截图。":::
1. 在“出站 IP”选项卡中，创建新的或选择某个现有的公共 IP。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-outbound-ip.png" alt-text="“创建 NAT 网关”中“出站 IP”选项卡的屏幕截图。":::
1. 在“子网”选项卡中，选择用于 VNet 集成的子网。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-subnet.png" alt-text="“创建 NAT 网关”中“子网”选项卡的屏幕截图。":::
1. 根据需要填写标记，并“创建”该 NAT 网关。 在预配 NAT 网关后，单击“转到资源组”，并选择新的 NAT 网关。 可以在“出站 IP”边栏选项卡中查看到应用将用于面向 Internet 的出站流量的公共 IP。
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-public-ip.png" alt-text="NAT 网关门户中“出站 IP”边栏选项卡的屏幕截图。"::: 

如果首选使用 CLI 来配置环境，以下是重要的命令。 作为先决条件，应该创建配置了 VNet 集成的 Web 应用。

确保为 VNet 集成配置“全部路由”（请注意：`az version` 的所需最小值为 2.27）：

```azurecli-interactive
az webapp config set --resource-group [myResourceGroup] --name [myWebApp] --vnet-route-all-enabled
```

创建公共 IP 和 NAT 网关：

```azurecli-interactive
az network public-ip create --resource-group [myResourceGroup] --name myPublicIP --sku standard --allocation static

az network nat gateway create --resource-group [myResourceGroup] --name myNATgateway --public-ip-addresses myPublicIP --idle-timeout 10
```

将 NAT 网关与 VNet 集成子网关联：

```azurecli-interactive
az network vnet subnet update --resource-group [myResourceGroup] --vnet-name [myVnet] --name [myIntegrationSubnet] --nat-gateway myNATgateway
```

## <a name="scaling-nat-gateway"></a>缩放 NAT 网关

同一 NAT 网关可以用于同一虚拟网络中的多个子网，允许在多个应用和应用服务计划中使用 NAT 网关。

NAT 网关支持公共 IP 地址和公共 IP 前缀。 NAT 网关可以最多支持单个 IP 地址和前缀中的 16 个 IP 地址。 每个 IP 地址分配 64,000 个端口（SNAT 端口），最多允许 1M 个可用端口。 在 NAT 网关的[缩放部分](../../virtual-network/nat-gateway/nat-gateway-resource.md#scaling)中了解详细信息。

## <a name="next-steps"></a>后续步骤
有关 NAT 网关的详细信息，请参阅 [NAT 网关文档](../../virtual-network/nat-gateway/nat-overview.md)。

有关 VNet 集成的详细信息，请参阅 [VNet 集成文档](../web-sites-integrate-with-vnet.md)。