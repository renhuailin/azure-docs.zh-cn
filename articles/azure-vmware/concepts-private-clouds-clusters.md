---
title: 概念-私有云和群集
description: 了解 Azure VMware 解决方案软件定义的数据中心和 vSphere 群集的主要功能。
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 7f8508abed3446c58a683454bc49ec7dbb49d051
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825075"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware 解决方案私有云和群集概念

Azure VMware 解决方案在 Azure 中提供基于 VMware 的私有云。 私有云包含用专用的裸机 Azure 主机构建的群集。 它们是通过 Azure 门户、CLI 或 PowerShell 进行部署和管理的。  私有云中预配的群集包括 VMware vSphere、vCenter、vSAN 和 NSX 软件。 Azure VMware 解决方案私有云硬件和软件部署在 Azure 中完全集成和自动化。

Azure 订阅、Azure VMware 解决方案私有云、vSAN 群集和主机之间存在逻辑关系。 此关系图显示单个 Azure 订阅，其中包含两个代表开发和生产环境的私有云。  其中每个私有云都是两个群集。 

本文介绍所有这些概念。

![客户订阅中两个私有云的图像](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>由于开发环境的潜在需求较低，因此请使用较小容量主机的小型群集。 

## <a name="private-clouds"></a>私有云

私有云包含用专用的裸机 Azure 主机构建的 vSAN 群集。 每个私有云可以有多个群集，这些群集由同一 vCenter 服务器和 NSX-T 管理器管理。 可以通过门户、CLI 或 PowerShell 部署和管理私有云。 

与其他资源一样，私有云在 Azure 订阅中进行安装和管理。 订阅中的私有云数量是可缩放的。 最初，每个订阅的私有云限制为一个。

## <a name="clusters"></a>群集
默认情况下，对于每个已创建的私有云，都有一个 vSAN 群集。 你可以使用 Azure 门户或通过 API 来添加、删除和缩放群集。  所有群集的默认大小均为三个主机，并且最多可以扩展到16台主机。  群集中使用的主机必须是相同的主机类型。

试用群集可用于评估并限制为三个主机。 每个私有云有一个试用群集。 你可以在评估期内按单个主机缩放试用群集。

你可以使用 vSphere 和 NSX-T 管理器来管理群集配置或操作的其他方面。 群集中每个主机的所有本地存储都受 vSAN 控制。

## <a name="hosts"></a>主机

Azure VMware 解决方案私有云群集使用超聚合、裸机基础结构主机。 下表显示了主机的 RAM、CPU 和磁盘容量。 

| 主机类型              |             CPU             |   RAM (GB)   |  vSAN NVMe 缓存层 (TB，原始)   |  vSAN SSD 容量层 (TB，原始)   |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
|  (他) High-End          |  双重 Intel 18 核 2.3 GHz  |     576      |                3.2               |                15.20               |

用于构建或缩放群集的主机来自主机的隔离池。 这些主机已通过硬件测试，并已安全删除所有数据。 

## <a name="vmware-software-versions"></a>VMware 软件版本

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="host-maintenance-and-lifecycle-management"></a>主机维护和生命周期管理

主机维护和生命周期管理对私有云群集的容量或性能没有影响。  自动主机维护的示例包括固件升级和硬件修复或更换。

Microsoft 负责对 NSX-T 设备进行生命周期管理，例如 NSX-T Manager 和 NSX-T 边缘。 它们还负责引导网络配置，例如创建第0层的网关，并启用 North-South 路由。 你负责 NSX-T SDN 配置。 例如，网络段、分布式防火墙规则、第1层网关和负载均衡器。

> [!IMPORTANT]
> 请勿修改 NSX-T 边缘或第0层网关的配置，因为这可能会导致服务丢失。

## <a name="backup-and-restoration"></a>备份和还原

私有云 vCenter 和 NSX-T 配置采用每小时备份计划。  备份保留三天。 如果需要从备份还原，请在 Azure 门户中打开 [支持请求](https://rc.portal.azure.com/#create/Microsoft.Support) 以请求还原。

## <a name="next-steps"></a>后续步骤

下一步是了解 [网络和互连的概念](concepts-networking.md)。

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

