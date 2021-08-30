---
title: 创建公共 IP 地址前缀 - Azure 门户
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure 门户创建公共 IP 地址。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/06/2021
ms.author: allensu
ms.openlocfilehash: 3a1dfefb034816038af4401a22ee87f7f1914c54
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598435"
---
# <a name="create-a-public-ip-address-prefix-using-the-azure-portal"></a>使用 Azure 门户创建公共 IP 地址前缀

了解公共 IP 地址前缀，以及如何创建、更改和删除此类地址。 公共 IP 地址前缀是一系列连续的标准 SKU 公共 IP 地址。 

创建公共 IP 地址资源时，可以从前缀分配一个静态公共 IP 地址，并将该地址关联到虚拟机、负载均衡器或其他资源。 有关详细信息，请参阅[公共 IP 地址前缀概述](public-ip-address-prefix.md)。

## <a name="prerequisites"></a>先决条件

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。

## <a name="create-a-public-ip-address-prefix"></a>创建公共 IP 地址前缀

在本部分中，将在 Azure 门户中创建公共 IP 前缀。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“公共 IP”。

3. 在搜索结果中时，选择“公共 IP 前缀”。

4. 选择“+ 新建”。 

5. 在“创建公共 IP 前缀”中，在“基本信息”选项卡中输入或选择以下值： 

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅 |
    | 资源组 | 选择“新建”。 </br> 输入“myResourceGroup”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 键入 myPublicIPprefix。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | IP 版本 | 保留默认值“IPv4”。 |
    | 前缀大小 | 选择前缀大小。 |

    :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ip-prefix.png" alt-text="在 Azure 门户中创建公共 IP 地址前缀" border="true":::
    
    > [!NOTE]
    >若要创建 IPv6 前缀，请选择“IPv6”作为“IP 版本”。 

     :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ipv6-prefix.png" alt-text="在 Azure 门户中创建公共 IPv6 地址前缀" border="true":::

6. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”蓝色按钮 。

7. 选择“创建”。

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>从前缀创建静态公共 IP 地址
在创建前缀后，必须从前缀创建静态 IP 地址。 在本部分，你将从先前创建的前缀创建一个静态 IP 地址。

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中时，选择“公共 IP 前缀”。

3. 在 “公共 IP 前缀”中，选择“myPublicIPPrefix”。 

4. 在 **MyPublicIPPrefix** **概述** 中，选择 **“+ 添加 IP 地址”** 。

    :::image type="content" source="./media/create-public-ip-prefix-portal/add-ip-address.png" alt-text="在 Azure 门户中创建前缀的公共 IP 地址" border="true":::

5. 在“名称”中，选择“myPublicIP” 。 

6. 至于其余设置，请保留默认值。

7. 选择 **添加** 。

    >[!NOTE]
    >仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。

8. 在设置中选择“公共 IP 地址”，以查看创建的 IP 地址。 
## <a name="delete-a-prefix"></a>删除前缀

本部分介绍如何删除前缀。

1. 在门户顶部的搜索框中，输入“公共 IP”。

2. 在搜索结果中时，选择“公共 IP 前缀”。

3. 在 “公共 IP 前缀”中，选择“myPublicIPPrefix”。 

4. 在“概述”部分选择“删除”。 

    >[!NOTE]
    >如果前缀中的地址关联到公共 IP 地址资源，必须先删除公共 IP 地址资源。 请参阅[删除公共 IP 地址](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)。

## <a name="clean-up-resources"></a>清理资源

在本文中，你创建了一个公共 IP 前缀，并从该前缀创建了一个公共 IP。 

完成公共 IP 前缀后，删除资源组及其包含的所有资源：

1. 搜索并选择“myResourceGroup”。

2. 选择“删除资源组”  。

3. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。
## <a name="next-steps"></a>后续步骤

- 了解使用[公共 IP 前缀](public-ip-address-prefix.md)的方案和好处
