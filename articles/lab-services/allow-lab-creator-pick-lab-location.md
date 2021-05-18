---
title: 允许实验室创建者在 Azure 实验室服务中选取位置
description: 本文介绍了实验室帐户管理员如何允许实验室创建者为实验室选取位置。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444193"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>允许实验室创建者在 Azure 实验室服务中为实验室选取位置
在 Azure 实验室服务中，实验室帐户所有者可以允许实验室创建者（教师）为其创建的实验室选取位置。 此位置可以不同于实验室帐户的位置。 位置是一组 Azure 区域。 例如，“美国”位置是一组区域，例如“美国东部”、“美国西部”，等等。 

作为实验室帐户所有者，你可以在创建实验室帐户时和创建实验室帐户后选择“允许实验室创建者选取实验室位置”选项（也可以对现有的实验室帐户这样做）。 

## <a name="at-the-time-of-lab-account-creation"></a>在创建实验室帐户时
创建实验室帐户时，你可以在第一个屏幕（“基本信息”选项卡）上看到此选项。 

![在创建实验室时启用此选项](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

如果你在“高级”选项卡中为实验室帐户选择了对等虚拟网络，则此选项会被禁用。  

![当启用了对等虚拟网络时](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>在创建实验室帐户后
创建实验室帐户后，可以按照以下步骤启用或禁用此选项： 

1. 在“实验室帐户”页的左侧菜单中选择“实验室设置”。
2. 如果要允许实验室创建者为实验室选择位置，请选择“允许实验室创建者选取实验室位置”选项。 如果此选项被禁用，则会自动在实验室帐户所在位置创建实验室。 
    
    为“对等虚拟网络”字段选择了某个虚拟网络时，此字段会被禁用。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域才能访问对等虚拟网络中的资源。 
1. 在工具栏上选择“保存”。 

    ![实验室设置](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>没有虚拟网络，不允许选择位置
在此场景中，你尚未启用“允许实验室创建者选取实验室位置”选项。 

![无实验室位置](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

那么，实验室创建者（教师）就看不到用于选取实验室位置的选项。 他们会看到的可供使用的选项是每种大小的每小时价格。 当他们创建实验室时，该实验室会创建在 Azure 区域中，其位置就是实验室帐户所在 Azure 区域的位置。 例如，如果实验室帐户在“美国西部”，则可能会在“美国中南部”创建实验室，但不会在“加拿大东部”创建实验室。 对于我们选择的区域，除了它应当位于该位置之外，我们无法保证任何事情。 如果某个大小当前受到约束，则实验室创建者会看到一个复选框，可以在其中查看通常受支持但当前不可用的大小。 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>在虚拟网络中，不允许选择位置
在此场景中，“允许实验室创建者选取实验室位置”选项被禁用，因为你为实验室帐户选择了一个对等虚拟网络。 然后，实验室创建者会看到与上一个选项相同的屏幕。 由于所有 VM 都必须与虚拟网络位于同一 Azure 区域中，因此会在虚拟网络所在的 Azure 区域中创建实验室。 如果该特定区域因某个大小而受到约束，则该大小将显示为不可用。 

## <a name="location-selection-is-enabled"></a>启用了位置选择功能
选择“允许实验室创建者选取实验室位置”时，实验室创建者（教师）在创建实验室时会看到用于选择位置的选项。 

![选取实验室位置](./media/allow-lab-creator-pick-lab-location/location-selection.png)

实验室创建者会看到提供该大小的所有位置的价格范围，并可选择一个位置。 将在映射到该位置的任何 Azure 区域中创建实验室。

如果某个位置受到约束，则默认情况下它不会显示在列表中。 展开下拉列表，然后选择“显示此大小的不可用位置”。 

![显示不可用位置](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
之前，定价基于你为实验室选择的 VM 大小。 现在，价格取决于操作系统 (OS)、大小和位置的组合。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [将实验室的网络连接到对等虚拟网络](how-to-connect-peer-virtual-network.md)
- [将共享映像库连接到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)