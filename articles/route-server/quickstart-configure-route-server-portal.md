---
title: 快速入门：使用 Azure 门户创建并配置路由服务器
description: 此快速入门介绍如何使用 Azure 门户来创建并配置路由服务器。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 36be075345dcacdf8246f46462a2665718f27e7e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424791"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建并配置路由服务器

本文可帮助你使用 Azure 门户将 Azure 路由服务器配置为与虚拟网络中的网络虚拟设备 (NVA) 对等互连。 Azure 路由服务器将会从 NVA 获知路由，并将这些路由配置到虚拟网络中的虚拟机上。 Azure 路由服务器还会将虚拟网络路由播发到 NVA。 有关详细信息，请阅读 [Azure 路由服务器](overview.md)。

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="使用 Azure 门户的路由服务器部署环境示意图。" border="false":::

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 查看 [Azure 路由服务器的服务限制](route-server-faq.md#limitations)。

## <a name="create-a-route-server"></a>创建路由服务器

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，并选择订阅。

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

### <a name="create-a-route-server"></a>创建路由服务器

1. 转到  https://aka.ms/routeserver 。

1. 选择“+ 新建路由服务器”。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="路由服务器登录页的屏幕截图。"::: 

1. 在“创建路由服务器”页上，输入或选择所需的信息。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="创建路由服务器页的屏幕截图。":::     

    | 设置 | 值 |
    |----------|-------|
    | 订阅 | 选择要用于部署路由服务器的 Azure 订阅。 |
    | 资源组 | 选择要在其中创建路由服务器的资源组。 如果目前没有资源组，可新建一个。 |
    | 名称 | 输入路由服务器的名称。 |
    | 区域 | 选择将在其中创建路由服务器的区域。 选择与之前创建的虚拟网络相同的区域，以在下拉列表中查看该虚拟网络。 |
    | 虚拟网络 | 选择将在其中创建路由服务器的虚拟网络。 可以创建新的虚拟网络，也可以使用现有的虚拟网络。 如果使用的是现有的虚拟网络，请确保现有虚拟网络具有足够的空间以至少容纳一个 /27 子网，以满足路由服务器子网的要求。 如果从下拉列表中看不到虚拟网络，请确保已选择正确的资源组或区域。 |
    | 子网 | 创建或选择虚拟网络后，将显示“子网”字段。 此子网专用于路由服务器。 选择“管理子网配置”并创建 Azure 路由服务器子网。 选择“+ 子网”并按照以下指南创建子网：</br><br>- 子网必须命名为 RouteServerSubnet。</br><br>- 子网必须为 /27 或更大。</br> |
    | 公共 IP 地址 | 创建新的或选择现有的标准公共 IP 资源以分配给路由服务器。 若要确保与管理路由服务器配置的后端服务的连接，需要公共 IP 地址。 |

1. 选择“查看 + 创建”，检查摘要，然后选择“创建” 。 

    > [!NOTE]
    > 部署路由服务器大约需要 20 分钟。

## <a name="set-up-peering-with-nva"></a>设置与 NVA 的对等互连

本部分将帮助你配置与 NVA 的 BGP 对等互连。

1. 在 Azure 门户中，转到[路由服务器](https://aka.ms/routeserver)，然后选择要配置的路由服务器。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="路由服务器列表的屏幕截图。"::: 

1. 在左侧导航面板中的“设置”下选择“对等方”。 然后选择“+ 添加”以添加新的对等。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="对等方登录页的屏幕截图。"::: 

1. 输入有关 NVA 对等的以下信息。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="“添加对等方”页的屏幕截图。":::

    | 设置 | 值 |
    |----------|-------|
    | 名称 | 为路由服务器与 NVA 之间的对等互连命名。 |
    | ASN |  输入 NVA 的自治系统编号 (ASN)。 |
    | IPv4 地址 | 输入路由服务器将与之通信的 NVA 的 IP 地址，以建立 BGP。 |

1. 选择“添加”以添加此对等方。

## <a name="complete-the-configuration-on-the-nva"></a>在 NVA 上完成配置

需要 Azure 路由服务器的对等 IP 和 ASN 才能在 NVA 上完成配置，以建立 BGP 会话。 可以从路由服务器的“概述”页中获取此信息。

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="路由服务器“概述”页的屏幕截图。":::

## <a name="configure-route-exchange"></a>配置路由交换

如果你有 ExpressRoute 网关和/或 VPN 网关，并且希望它们与路由服务器交换路由，则可以启用路由交换。

1. 在 Azure 门户中，转到[路由服务器](https://aka.ms/routeserver)，然后选择要配置的路由服务器。

1. 在左侧导航面板的“设置”下选择“配置”。

1. 为“分支到分支”设置选择“启用”，然后选择“保存”  。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="如何启用路由交换的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要 Azure 路由服务器，请在“概述”页中选择“删除”以取消设置路由服务器。

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="如何删除路由服务器的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

在创建 Azure 路由服务器后，请继续了解 Azure 路由服务器如何与 ExpressRoute 和 VPN 网关进行交互： 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 和 Azure VPN 支持](expressroute-vpn-support.md)
