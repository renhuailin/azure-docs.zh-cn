---
title: 监视 Azure 上的 SAP HANA（大型实例）| Microsoft Docs
description: 了解如何监视 Azure 上的 SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2021
ms.author: madhukan
ms.custom:
- H1Hack27Feb2017
- contperf-fy21q4
ms.openlocfilehash: 3c77fe23b7ce058f31a216c5991b7129c3ac99fe
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579821"
---
# <a name="monitor-sap-hana-large-instances-on-azure"></a>监视 Azure 上的 SAP HANA（大型实例）

本文将介绍监视 SAP HANA 大型实例（也称为 BareMetal 基础结构）。

Azure 上的 SAP HANA（大型实例）与任何其他 IaaS 部署没有区别。 监视操作系统和应用程序非常重要。 你需要了解应用程序如何使用以下资源：

- CPU
- 内存
- 网络带宽
- 硬盘空间

监视 SAP HANA 大型实例以查看上述资源是否充足，或者这些资源是否正在耗尽。 以下部分更详细地介绍每个资源。

## <a name="cpu-resource-consumption"></a>CPU 资源消耗

SAP 定义 SAP HANA 工作负荷的 CPU 使用的最大阈值。 保持在此阈值范围内可确保有足够的 CPU 资源来处理存储在内存中的数据。 由于缺少索引或类似问题，SAP HANA 服务执行查询时，可能会出现高 CPU 消耗。 因此，监视 HANA 大型实例的 CPU 消耗和特定 HANA 服务的 CPU 消耗至关重要。

## <a name="memory-consumption"></a>内存占用率 

在 SAP HANA 大型实例上，监视 HANA 和 HANA 外的内存消耗非常重要。 监视数据使用 HANA 分配的内存的方式，以便可以保持在 SAP 的调整大小准则范围内。 监视大型实例上的内存消耗，确保非 HANA 软件不会消耗过多的内存。 应避免非 HANA 软件与 HANA 争用内存。

## <a name="network-bandwidth"></a>网络带宽 

Azure 虚拟网络 (VNet) 网关的带宽受到限制。 只有如此多的数据可以移入 Azure VNet。 监视 VNet 中的所有 Azure VM 接收的数据。 通过这种方式，你将知道何时接近所选 Azure 网关 SKU 的限制。 还可以监视 HANA 大型实例上的传入和传出网络流量，以跟踪在一段时间内处理的卷。

## <a name="disk-space"></a>磁盘空间

磁盘空间消耗量通常会随着时间的推移而增大。 常见原因包括：
- 数据量随时间推移增加
- 执行事务日志备份
- 存储跟踪文件
- 拍摄存储快照 

因此，必须监视磁盘空间用量，并且管理与 HANA 大型实例相关的磁盘空间。

## <a name="preloaded-system-diagnostic-tools"></a>预加载的系统诊断工具

对于 HANA 大型实例类型 II SKU，服务器附带预载的系统诊断工具。 你可以使用这些诊断工具进行系统运行状况检查。
 
运行以下命令，在 /var/log/health_check 生成健康状况检查日志文件。

```
/opt/sgi/health_check/microsoft_tdi.sh
```
同 Microsoft 支持团队一起排除故障时，可能需要使用这些诊断工具提供日志文件。 可以使用该命令压缩文件：

```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

## <a name="azure-monitor-for-sap-solutions"></a>适用于 SAP 的 Azure Monitor 解决方案

你可以使用 Azure Monitor for SAP Solutions 来监视上面列出的所有资源及更多内容。 Azure Monitor for SAP Solutions 是 Azure 原生的。 你可以使用它将 Azure 基础结构和数据库中的数据收集到一个位置，并以可视方式关联数据，从而更快地进行故障排除。 有关详细信息，请参阅 [Azure Monitor for SAP Solutions](../../../virtual-machines/workloads/sap/azure-monitor-overview.md)。

## <a name="next-steps"></a>后续步骤

了解如何在 SAP HANA 中进行监视和故障排除。

> [!div class="nextstepaction"]
> [HANA 端的监视和故障排除](hana-monitor-troubleshoot.md)
