---
title: 将子网委派给 Azure NetApp 文件 | Microsoft Docs
description: 了解如何将子网委托给 Azure NetApp 文件。 在创建卷时，需要指定委托的子网。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/25/2021
ms.author: b-juche
ms.openlocfilehash: cdb184d4b96e4cfee2b5450f35c947efb768da9b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866974"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>将子网委派给 Azure NetApp 文件 

必须将一个子网委派给 Azure NetApp 文件。   在创建卷时，需要指定委派的子网。

## <a name="considerations"></a>注意事项

* 用于创建新子网的向导默认设置 /24 网络掩码，这将提供 251 个可用 IP 地址。 对于大多数用例，使用 /28 网络掩码（提供 11 个可用 IP 地址）就足够了。 在预计有许多卷和存储终结点的场景（如 SAP HANA）中，应该考虑使用较大的子网（例如 /26 网络掩码）。 如果不需要在 Azure 虚拟网络 (VNet) 中保留许多客户端或 VM IP 地址，也可以使用向导建议的默认网络掩码 /24。 请注意，初始创建后无法更改委托网络的网络掩码。 
* 在每个 VNet 中，只能将一个子网委派给 Azure NetApp 文件。   
   Azure 允许你在一个 VNet 中创建多个委托子网。  但是，如果使用多个委托子网，则任何创建新卷的尝试都将失败。  
   一个 VNet 中只能有一个委托子网。 一个 NetApp 帐户可以将卷部署到多个 VNet 中，每个 VNet 都有自己的委托子网。  
* 不能在委派的子网中指定网络安全组或服务终结点。 这样做会导致子网委派失败。
* 当前不支持从全局对等互连的虚拟网络访问卷。
* 适用于 Azure NetApp 文件的委托子网不支持[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (UDR) 和网络安全组 (NSG)。 但是，你可以将 UDR 和 NSG 应用于其他子网，即使这些子网与委派给 Azure NetApp 文件的子网位于同一 VNet 中也可如此。  
   Azure NetApp 文件会创建到委托子网的系统路由。 如果需要该路由以进行故障排除，该路由会显示在路由表的“有效路由”中。

## <a name="steps"></a>步骤

1.  在 Azure 门户中，转到“虚拟网络”边栏选项卡，选择要用于 Azure NetApp 文件的虚拟网络。    

1. 从“虚拟网络”边栏选项卡中选择“子网”，单击“+子网”按钮。 

1. 在“添加子网”页面中完成以下必需字段来新建要用于 Azure NetApp 文件的子网：
    * 名称：指定子网名称。
    * 地址范围：指定 IP 地址范围。
    * 子网委派：选择“Microsoft.NetApp/volumes”。 

      ![子网委派](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
还可以在[为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)时创建和委派子网。 

## <a name="next-steps"></a>后续步骤

* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
* [了解 Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
