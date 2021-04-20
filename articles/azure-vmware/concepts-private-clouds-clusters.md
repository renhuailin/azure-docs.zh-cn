---
title: 概念 - 私有云和群集
description: 了解 Azure VMware 解决方案软件定义的数据中心和 vSphere 群集的主要功能。
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: aff66e01ae4b056eb082d2000611718b1a5cf66a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773908"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware 解决方案私有云和群集概念

Azure VMware 解决方案在 Azure 中提供基于 VMware 的私有云。 私有云包含通过专用裸机 Azure 主机生成的群集。 它们通过 Azure 门户、CLI 或 PowerShell 进行部署和管理。  私有云中预配的群集包括 VMware vSphere、vCenter、vSAN 和 NSX 软件。 Azure VMware 解决方案私有云硬件和软件部署在 Azure 中实现了完全集成和自动化。

Azure 订阅、Azure VMware 解决方案私有云、vSAN 群集和主机之间存在逻辑关系。 此关系图显示了一个 Azure 订阅，其中包含两个代表开发和生产环境的私有云。  其中每个私有云都是两个群集。 

本文将介绍所有这些概念。

![某一客户订阅中两个私有云的图像](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>私有云

私有云包含通过专用裸机 Azure 主机生成的 vSAN 群集。 每个私有云可以有多个群集，这些群集由同一 vCenter 服务器和 NSX-T Manager 托管。 可以通过门户、CLI 或 PowerShell 部署和管理私有云。 

与其他资源一样，私有云在 Azure 订阅中进行安装和管理。 订阅中的私有云数量是可缩放的。 最初，每个订阅的私有云数限制为一个。

## <a name="clusters"></a>群集
默认情况下，对于创建的每个私有云，均有一个 vSAN 群集。 可以使用 Azure 门户或通过 API 来添加、删除和缩放群集。  所有群集的默认大小均为 3 台主机，并且最多可以扩展到 16 台主机。 每个私有云最多可以有 4 个群集。

试用群集可用于评估并限制为 3 台主机。 每个私有云有一个试用群集。 可以在评估期内按单个主机缩放试用群集。

可以使用 vSphere 和 NSX-T Manager 来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

## <a name="hosts"></a>主机

Azure VMware 解决方案群集基于超融合裸机基础结构。 下表显示了主机的 RAM、CPU 和磁盘容量。

| 主机类型              |             CPU             |   RAM (GB)   |  vSAN NVMe 缓存层（TB，原始）  |  vSAN SSD 容量层（TB，原始）  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  双 Intel 18 核心 2.3 GHz  |     576      |                3.2               |                15.20               |

用于生成或缩放群集的主机来自独立的主机池。 这些主机已通过硬件测试，并已安全删除所有数据。 

## <a name="vmware-software-versions"></a>VMware 软件版本

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="update-frequency"></a>更新频率

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>主机维护和生命周期管理

主机维护和生命周期管理对私有云群集的容量或性能没有影响。  自动主机维护的示例包括固件升级和硬件修复或更换。

Microsoft 负责对 NSX-T 设备（如 NSX-T Manager 和 NSX-T Edge）进行生命周期管理。 Microsoft 负责启动网络配置，例如创建第 0 层的网关和启用北-南路由。 你负责 NSX-T SDN 配置。 例如，网络段、分布式防火墙规则、第 1 层网关和负载均衡器。

## <a name="backup-and-restoration"></a>备份和还原

私有云 vCenter 和 NSX-T 配置采用每小时备份计划。  备份会保留三天。 如果需要从备份恢复，请在 Azure 门户中打开[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)以请求恢复。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure VMware 解决方案私有云概念，建议你了解以下内容： 

- [Azure VMware 解决方案网络和互连概念](concepts-networking.md)。
- [Azure VMware 解决方案存储概念](concepts-storage.md)。
- [如何启用 Azure VMware 解决方案资源](enable-azure-vmware-solution.md)。

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

