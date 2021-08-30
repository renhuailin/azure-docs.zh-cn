---
title: 概念 - 网络互连
description: 了解 Azure VMware 解决方案中网络和互连的关键方面和用例。
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 91bd1bfc207a2fb08b644e4ecf54be7b254c39fa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071965"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware 解决方案网络和互连概念

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Azure VMware 解决方案私有云中有两种互连方式：

- [仅限 Azure 的基本互连](#azure-virtual-network-interconnectivity)，通过此方式可仅在 Azure 中使用单个虚拟网络来管理和使用私有云。 此实现最适合不需要从本地环境访问的 Azure VMware 解决方案评估或实现。

- [本地到私有云的完整互连](#on-premises-interconnectivity)扩展了仅限 Azure 的基本实现，使其包括本地与 Azure VMware 解决方案私有云之间的互连。
 
本文介绍建立网络和互连的关键概念，包括要求和限制。 此外，本文还提供了使用 Azure VMware 解决方案配置网络时需要了解的信息。

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware 解决方案私有云用例

Azure VMware 解决方案私有云的用例包括：
- 云中的新 VMware VM 工作负载
- VM 工作负载迸发到云（仅限本地到 Azure VMware 解决方案）
- VM 工作负载迁移到云（仅限本地到 Azure VMware 解决方案）
- 灾难恢复（Azure VMware 解决方案到 Azure VMware 解决方案或本地到 Azure VMware 解决方案）
- Azure 服务的消耗

> [!TIP]
> Azure VMware 解决方案服务的所有用例都允许使用本地到私有云的连接。

## <a name="azure-virtual-network-interconnectivity"></a>Azure 虚拟网络互连

可以将 Azure 虚拟网络与 Azure VMware 解决方案私有云互连。 可以管理 Azure VMware 解决方案私有云，使用私有云中的工作负载，以及访问其他 Azure 服务。

下图显示了在部署私有云时建立的基本网络互连。 其中显示了 Azure 中的虚拟网络和私有云之间的逻辑网络。 此连接是通过后端 ExpressRoute 建立的，它是 Azure VMware 解决方案服务的一部分。 互连满足以下主要用例：

- 对可从 Azure 订阅中的 VM 访问的 vCenter 服务器和 NSX-T 管理器的入站访问。
- 从私有云上的 VM 到 Azure 服务的出站访问。
- 对私有云中运行的工作负载的入站访问。


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="此图显示了在 Azure VMware 解决方案私有云部署时建立的基本网络互连。" border="false":::

## <a name="on-premises-interconnectivity"></a>本地互连

在完全互连的方案中，可以从 Azure 虚拟网络和本地访问 Azure VMware 解决方案。 此实现是上一节中所述的基本实现的扩展。 需要 ExpressRoute 线路从本地连接到 Azure 中的 Azure VMware 解决方案私有云。

下图显示了本地到私有云的互连，它使得以下用例可以实现：

- 本地和 Azure VMware 解决方案之间的热/冷 vCenter vMotion。
- 本地到 Azure VMware 解决方案私有云的管理访问。

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="此图显示了虚拟网络和本地与私有云的互连。" border="false":::

为了与私有云完全互连，需要启用 ExpressRoute Global Reach，然后在 Azure 门户中为 Global Reach 请求授权密钥和专用对等互连 ID。 授权密钥和对等互连 ID 用于在订阅中的 ExpressRoute 线路和私有云的 ExpressRoute 线路之间建立 Global Reach。 链接后，这两条 ExpressRoute 线路将本地环境之间的网络流量路由到私有云。 有关这些过程的详细信息，请参阅[教程：创建与私有云对等互连的 ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)。

## <a name="limitations"></a>限制
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>后续步骤 

现在你已经了解了 Azure VMware 解决方案网络和互连概念，你可能想要了解：

- [Azure VMware 解决方案存储概念](concepts-storage.md)
- [Azure VMware 解决方案标识概念](concepts-identity.md)
- [启用 Azure VMware 解决方案资源提供程序](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management
[concepts-storage]: ./concepts-storage.md
