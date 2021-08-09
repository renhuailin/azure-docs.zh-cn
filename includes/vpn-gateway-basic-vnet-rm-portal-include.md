---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 82db4939b6f980e1d9fb9a82a6cd255bb10c04cb
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126335"
---
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“搜索资源、服务和文档(G+/)”中，键入“虚拟网络”。  

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png" alt-text="屏幕截图显示 Azure 门户的搜索栏。" border="false":::
1. 从“市场”结果中选择“虚拟网络”。  

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png" alt-text="屏幕截图显示 Azure 门户的搜索栏结果，同时从“市场”中选择了“虚拟网络”。" border="false":::
1. 在“虚拟网络”页上选择“创建”。  

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png" alt-text="屏幕截图显示“虚拟网络”页并选择了“创建”按钮。" border="false":::
1. 选择“创建”后，会打开“创建虚拟网络”页。  
1. 在“基本信息”选项卡上，配置“项目详细信息”和“实例详细信息”VNet 设置。   

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png" alt-text="屏幕截图显示“基本信息”选项卡。" border="false":::

   在填写字段时，如果在字段中输入的字符通过了验证，则会出现绿色的对钩标记。 某些值是自动填写的，你可以将其替换为自己的值：

   - **订阅**：确认列出的订阅是正确的。 可以使用下拉列表更改订阅。
   - **资源组**：选择现有资源组，或单击“新建”以创建新资源组  。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/management/overview.md#resource-groups)。
   - **名称**：输入虚拟网络的名称。
   - **区域**：选择 VNet 的位置。 该位置确定要部署到此 VNet 的资源将位于哪里。

1. 在“IP 地址”选项卡上配置值。  以下示例中显示的值用于演示目的。 根据所需的设置调整这些值。

   :::image type="content" source="./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png" alt-text="屏幕截图显示“IP 地址”选项卡。" border="false"::: 
   - **IPv4 地址空间**：默认情况下，系统会自动创建一个地址空间。 可以单击该地址空间，将其调整为反映你自己的值。 还可以添加更多的地址空间。
   - **子网**：如果你使用默认地址空间，则会自动创建一个默认子网。 如果你更改地址空间，则需要添加一个子网。 选择“+添加子网”，打开“添加子网”窗口 。 配置以下设置，然后选择“添加”来添加值：
      - **子网名称**：在本例中，我们已将子网命名为“FrontEnd”。
      - **子网地址范围**：此子网的地址范围。

1. 暂时在“安全”选项卡上保留默认值：

   - DDos 防护：已禁用
   - **防火墙**：已禁用
1. 选择“审阅 + 创建”，验证虚拟网络设置。
1. 验证设置后，选择“创建”。
