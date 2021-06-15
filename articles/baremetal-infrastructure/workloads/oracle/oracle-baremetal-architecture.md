---
title: 适用于 Oracle 的 BareMetal 基础结构的体系结构
description: 了解适用于 Oracle 的 BareMetal 基础结构的多个配置的体系结构。
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: 1e4a00f53018647ca3b3528a9881ec36af067e28
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578630"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>适用于 Oracle 的 BareMetal 基础结构的体系结构

在本文中，我们将探讨适用于 Oracle 的 BareMetal 基础结构的体系结构选项以及各自支持的功能。

## <a name="single-instance"></a>单实例

此拓扑支持 Oracle Database 单实例，其中包含用于迁移到 BareMetal 基础结构的 Oracle Data Guard。 它支持使用备用节点来实现高可用性和维护工作。

[![显示具有 Oracle Data Guard 的 Oracle Database 单实例的体系结构的图。](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) One Node

此拓扑支持具有共享存储和 GRID 群集的 RAC 配置。 数据库实例仅在单节点上运行（主动-被动配置）。

功能包括：

- 主动-被动，采用 Oracle RAC One Node

    - 自动故障转移

    - 在第二个节点上快速重启

- 实时故障转移和可伸缩性，采用 Oracle RAC

- 零停机时间滚动维护

[![显示 Oracle RAC One Node 主动-被动配置的体系结构的图。](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>RAC

此拓扑支持具有共享存储和 Grid 群集的 Oracle RAC 配置，而每个数据库的多个实例同时运行（主动-主动配置）。

- 通过联机预配添加的服务器，可轻松缩放性能。 
-  用户在所有服务器上都处于活动状态，并且所有服务器都共享对同一 Oracle Database 的访问权限。 
-  所有类型的数据库维护都可以联机或以滚动方式执行，以便实现最小或零停机时间。 
- Oracle Active Data Guard (ADG) 备用系统可以轻松地将双用途用作测试系统。 

此配置使你可以先在生产数据库的精确副本上测试所有更改，然后再应用于生产环境。

> [!NOTE]
> 如果要使用 Active Data Guard Far Sync（同步模式），则需要考虑支持此功能的区域性区域。 仅对于地理上分散的区域，才建议使用具有异步模式的 Data Guard。

[![显示 Oracle RAC 主动-主动配置的体系结构的图。](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>后续步骤

了解如何为 Oracle 工作负载预配 BareMetal 实例。

> [!div class="nextstepaction"]
> [预配适用于 Oracle 的 BareMetal 基础结构](oracle-baremetal-provision.md)

