---
title: 配置网络路由首选项
titleSuffix: Azure Storage
description: 为 Azure 存储帐户配置网络路由首选项，以指定如何通过 Internet 从客户端将网络流量路由到你的帐户。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700788"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>配置 Azure 存储的网络路由首选项

本文介绍如何为存储帐户配置网络路由首选项和特定于路由的终结点。 

网络路由首选项指定如何通过 internet 从客户端将网络流量路由到你的帐户。 路由特定的终结点是 Azure 存储为你的存储帐户创建的新终结点。 这些终结点将流量路由到所需的路径，而无需更改默认路由首选项。 若要了解详细信息，请参阅 [Azure 存储的网络路由首选项](network-routing-preference.md)。

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>为默认公用终结点配置路由首选项

默认情况下，存储帐户的公共终结点的路由首选项设置为 "Microsoft 全局网络"。 可以在 Microsoft 全球网络和 Internet 路由之间进行选择，作为存储帐户公共终结点的默认路由首选项。 若要详细了解这两种类型路由之间的差异，请参阅 [Azure 存储的网络路由首选项](network-routing-preference.md)。 

将路由首选项更改为 Internet 路由：

1. 导航到门户中的存储帐户。

2. 在 " **设置**" 下，选择 " **网络**"。

    > [!div class="mx-imgBorder"]
    > ![网络菜单选项](./media/configure-network-routing-preference/networking-option.png)

3.  在 " **防火墙和虚拟网络** " 选项卡上的 " **网络路由**" 下，将 **路由首选项** 设置更改为 **Internet 路由**。

4.  单击“ **保存**”。

    > [!div class="mx-imgBorder"]
    > ![internet 路由选项](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>配置特定于路由的终结点

你还可以配置特定于路由的终结点。 例如，你可以将默认终结点的路由首选项设置为 *Internet 路由*，然后发布路由特定的终结点，以便在 Internet 上的客户端与你的存储帐户之间通过 Microsoft 全球网络路由流量。

1.  导航到门户中的存储帐户。

2.  在 " **设置**" 下，选择 " **网络**"。

3.  在 " **防火墙和虚拟网络** " 选项卡中，在 " **发布路由特定终结点**" 下，选择路由特定终结点的路由首选项，然后单击 " **保存**"。 此首选项只影响特定于路由的终结点。 此首选项不影响默认路由首选项。  

    下图显示了所选的 **Microsoft 网络路由** 选项。

    > [!div class="mx-imgBorder"]
    > ![Microsoft 网络路由选项](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>查找特定于路由的终结点的终结点名称

如果配置了特定于路由的终结点，则可以在存储帐户的属性中找到终结点。

1.  在“设置”下面，选择“属性”。

    > [!div class="mx-imgBorder"]
    > ![属性菜单选项](./media/configure-network-routing-preference/properties.png)

2.  为支持路由首选项的每个服务显示 **Microsoft 网络路由** 终结点。 此图显示了 blob 和文件服务的终结点。

    > [!div class="mx-imgBorder"]
    > ![用于路由特定终结点的 Microsoft 网络路由选项](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>另请参阅

- [网络路由首选项](network-routing-preference.md)
- [配置 Azure 存储防火墙和虚拟网络](storage-network-security.md)
- [有关 Blob 存储的安全性建议](../blobs/security-recommendations.md)
