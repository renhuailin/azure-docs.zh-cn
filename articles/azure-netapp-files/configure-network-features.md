---
title: 为 Azure NetApp 文件卷配置网络功能 | Microsoft Docs
description: 说明网络功能的选项，以及如何为卷配置网络功能选项。
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
ms.date: 10/04/2021
ms.custom: references_regions
ms.author: b-juche
ms.openlocfilehash: 6350084ef916132d7b4d77da00853836daeabccd
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538996"
---
# <a name="configure-network-features-for-an-azure-netapp-files-volume"></a>为 Azure NetApp 文件卷配置网络功能

网络功能已提供公共预览版。  此功能可用于指示是否要将 VNet 功能用于 Azure NetApp 文件卷。 借助此功能，可将选项设置为“标准”**或“基本”**。 可在创建新的 NFS、SMB 或双协议卷时指定设置。 有关网络功能的详细信息，请参阅 [Azure NetApp 文件网络规划指南](azure-netapp-files-network-topologies.md)。

本文可帮助你了解这些选项，并将介绍如何配置网络功能。

## <a name="options-for-network-features"></a>网络功能选项 

网络功能存在以下两个选项： 

* ***标准***  
    此设置可为卷启用 VNet 功能。  

    如果需要更高的 IP 限制或 VNet 功能（如[网络安全组](../virtual-network/network-security-groups-overview.md)、[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)或其他连接模式），应将“网络功能”设置为“标准”。

* ***基本***  
    此设置将减少 IP 限制次数 (<1000)，并且没有适用于卷的其他 VNet 功能。

    如果无需使用 VNet 功能，应将“网络功能”设置为“基本”。  

## <a name="supported-regions"></a>支持的区域 

目前，以下区域可支持网络功能： 

* 美国中北部 

## <a name="considerations"></a>注意事项

* 无论“网络功能”选项的设置为何（“标准”或“基本”），Azure VNet 只能将一个子网委托给 Azure NetApp 文件。 请参阅[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md#considerations)。 
 
* 目前，只能在新建卷的过程中指定网络功能设置， 而不能修改现有卷中的设置。 

* 只有在相应的 [Azure 区域支持标准卷功能](#supported-regions)时，才能创建具有标准网络功能的卷。 
    * 如果该区域支持标准卷功能，则“创建卷”页面的“网络功能”字段将默认为“标准”， 且可将此设置更改为“基本”。 
    * 如果该区域不支持标准卷功能，则“创建卷”页面的“网络功能”字段将默认为“基本”，且可修改此设置。

* 查找与所需网络功能类型兼容的存储，该能力取决于指定的 VNet。  如果因资源不足而无法创建卷，可尝试其他兼容存储可用的 VNet。
  
## <a name="register-the-feature"></a>注册功能 

网络功能目前以公共预览版提供。 如果你是首次使用此功能，则需要先注册该功能。

1.  运行以下命令注册功能：

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > RegistrationState 可能会处于 `Registering` 状态长达 60 分钟，然后才更改为 `Registered` 状态。 请等到状态变为`Registered`后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

此外，[Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 和 `az feature show` 分别可用于注册功能和显示注册状态。 

## <a name="set-the-network-features-option"></a>设置“网络功能”选项

本部分将介绍如何设置“网络功能”选项。 

1. 在创建新的 [NFS](azure-netapp-files-create-volumes.md)、[SMB](azure-netapp-files-create-volumes-smb.md) 或[双协议](create-volumes-dual-protocol.md)卷的过程中，可以在“创建卷”界面的“基本”选项卡下将“网络功能”选项设置为“基本”或“标准”。

    以下屏幕截图显示了在支持标准网络功能的区域创建卷的示例： 

    ![屏幕截图：创建支持标准网络功能的卷。](../media/azure-netapp-files/network-features-create-standard.png)

    以下屏幕截图显示了在不支持标准网络功能的区域创建卷的示例： 

    ![屏幕截图：创建支持基本网络功能的卷。](../media/azure-netapp-files/network-features-create-basic.png)

2. 在完成创建卷的过程之前，可以在“创建卷”界面的“查看 + 创建”选项卡中显示指定的网络功能设置。 单击“创建”即可创建卷。

    ![屏幕截图：创建卷时的“查看 + 创建”选项卡。](../media/azure-netapp-files/network-features-review-create-tab.png)

3. 单击“卷”即可显示每个卷的网络功能设置：

    [![屏幕截图：显示网络功能设置的“卷”页面。](../media/azure-netapp-files/network-features-volume-list.png)](../media/azure-netapp-files/network-features-volume-list.png#lightbox)

## <a name="next-steps"></a>后续步骤  

* [Azure NetApp 文件网络规划指南](azure-netapp-files-network-topologies.md)
* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md) 
* [为 Azure NetApp 文件创建双重协议卷](create-volumes-dual-protocol.md) 