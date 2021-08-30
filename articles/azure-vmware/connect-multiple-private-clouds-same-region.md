---
title: 连接同一区域中的多个 Azure VMware 解决方案私有云（预览版）
description: 了解如何在位于同一区域中的两个或多个 Azure VMware 解决方案私有云之间创建网络连接。
ms.topic: how-to
ms.date: 07/02/2021
ms.openlocfilehash: 562980ce52216f892cb10689206211e23f3c8886
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586718"
---
# <a name="connect-multiple-azure-vmware-solution-private-clouds-in-the-same-region-preview"></a>连接同一区域中的多个 Azure VMware 解决方案私有云（预览版）

使用 AVS 互连功能可在位于同一区域中的两个或多个 Azure VMware 解决方案私有云之间创建网络连接。 此功能可在私有云的管理网络和工作负载网络之间创建路由链接，以在云之间启用网络通信。

可以将某个私有云连接到多个私有云，并且这些连接是不可转移的。 例如，如果私有云 1 连接到私有云 2，并且私有云 2 连接到私有云 3，则私有云 1 和 3 在建立直接连接之前不会进行通信   。

只能连接同一区域中的私有云。 若要连接不同区域中的私有云，请[使用 ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) 连接私有云并采用将私有云连接到本地线路的相同方式进行连接。 

>[!IMPORTANT]
>AVS 互连（预览版）功能目前处于公共预览状态。  
>此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="supported-regions"></a>支持的区域

AVS 互连（预览版）功能在除美国中南部 (SAT20)、欧洲北部 (DUB21)、东南亚 (SG2) 和英国西部 (CWL20) 外的所有区域供应。 

## <a name="prerequisites"></a>先决条件

- 要连接的每个私有云的写入访问权限
- 每个云中的路由 IP 地址空间都是唯一的，不会重叠

>[!NOTE]
>在创建对等互连之前，AVS 互连功能不会像对本机 Azure vNet 对等互连那样检查 IP 空间是否重叠。 因此，你需负责确保私有云之间没有重叠。
>
>在 Azure VMware 解决方案环境中，可以在非路由到 Azure 的 NSX 网段上配置非路由的重叠 IP 部署。  这不会导致 AVS 互连功能出现问题，因为它仅在每个私有云上的 NSX T0 之间路由。


## <a name="add-connection-between-private-clouds"></a>添加私有云之间的连接

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“连接” 。

2. 选择“AVS 互连”选项卡，然后选择“添加” 。

   :::image type="content" source="media/networking/private-cloud-to-private-cloud-no-connections.png" alt-text="显示“连接”下的“AVS 互连”选项卡的屏幕截图。" border="true" lightbox="media/networking/private-cloud-to-private-cloud-no-connections.png":::

3. 选择信息和 Azure VMware 解决方案私有云以建立新连接。

   >[!NOTE]
   >只能连接到同一区域中的私有云。 若要连接到不同区域中的私有云，请[使用 ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) 连接私有云并采用将私有云连接到本地线路的相同方式进行连接。 

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud.png" alt-text="显示了添加到其他私有云的连接所需的信息。" border="true":::


4. 选中“我确认”复选框，表示确认两个私有云中没有重叠的路由 IP 空间。 

5. 选择“创建”。  可以查看连接的创建状态。

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud-notification.png" alt-text="显示关于正在进行的连接和现有连接的通知信息。" border="true":::

   你将看到“AVS 私有云”下的所有连接。
   
   :::image type="content" source="media/networking/private-cloud-to-private-cloud-two-connections.png" alt-text="屏幕截图显示了“连接”下的“AVS 互连”选项卡和两个已建立的私有云连接。" border="true" lightbox="media/networking/private-cloud-to-private-cloud-two-connections.png":::


## <a name="remove-connection-between-private-clouds"></a>删除私有云之间的连接

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“连接” 。

2. 对于要删除的连接，在右侧选择“删除”（垃圾桶），然后选择“是” 。


## <a name="next-steps"></a>后续步骤

现在，你已连接同一区域中的多个私有云，你可能想要了解以下操作：

- [将 Azure VMware 解决方案资源移动到另一个区域](move-azure-vmware-solution-across-regions.md)
- [将 Azure VMware 解决方案订阅移动到另一个订阅](move-ea-csp-subscriptions.md)
