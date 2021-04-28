---
title: Azure VMware Solution by CloudSimple 2020 年 9 月更新
description: 本文介绍在此维护操作期间将发生的事件以及对私有云的更改。
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89447792"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution by CloudSimple 2020 年 9 月更新

对 Azure VMware Solution 服务的重要更新将在 9 月执行。 在维护过程中发送的电子邮件通知将包括维护的时间线。 本文介绍在此维护操作期间将发生的事件以及对私有云的更改。

> [!NOTE]
> 此维护是非中断性升级。 在升级过程中，你可能会看到其中一个冗余组件停止运行。

## <a name="vmware-infrastructure-upgrade"></a>VMware 基础结构升级

私有云的 VMware 基础结构将更新为较新的版本。 这包括对私有云的 vCenter、ESXi、NSX-T 和混合云扩展 (HCX) 组件（如果已部署）的更新。

在升级过程中，一个新节点将添加到私有云中，然后一个节点将被置于维护模式下以进行升级操作。 这可以确保在升级过程中保持私有云的容量和可用性。 在升级 VMware 组件期间，你可能会看到 vCenter Web UI 上显示警报。 警报是服务操作团队执行的维护操作的一部分。

组件版本

- ESXi 6.7U3
- vCenter 6.7U3
- vSAN 6.7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>数据中心更新

此更新包括对数据中心基础结构的更新。 在维护期间将执行非中断性更新。 在更新过程中，你会注意到冗余减少。 可能会在私有云 VMware 基础结构、ExpressRoute 线路、GlobalReach 连接以及任何与链接可用性之一相关的站点到站点 VPN 设备上生成警报。 在更新过程中，这是正常现象，因为组件将作为更新的一部分重启。

-   如果站点到站点 VPN 作为单个实例（非 HA）部署，则可能需要重新建立 VPN 连接。

-   如果使用点到站点 VPN 连接，则必须重新建立 VPN 连接。

## <a name="post-update"></a>发布更新

更新完成后，应会看到更新版本的 VMware 组件。 如果你注意到任何问题或有任何疑问，请创建[支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，联系我们的支持团队。
