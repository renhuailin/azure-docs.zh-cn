---
title: Azure 负载均衡器中的新增功能
description: 了解 Azure 负载均衡器的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将发布的更改。
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a132b97822b9c4ca8f6f14b0b45cebc2775fdc86
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291097"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure 负载均衡器中的新增功能有哪些？

Azure 负载均衡器会定期更新。 随时了解最新公告。 本文提供以下事项的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能（如果适用）

你还可以[在此处](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)找到最新的 Azure 负载均衡器更新并订阅 RSS 源。

## <a name="recent-releases"></a>最新版本

| 类型 |名称 |说明  |添加日期  |
| ------ |---------|---------|---------|
| Feature | [支持基于 IP 的后端池（正式发布版）](https://azure.microsoft.com/updates/iplbga/) | Azure 负载均衡器支持通过 IPv4 或 IPv6 地址从后端池中添加和删除资源。 这样就可以轻松管理与负载均衡器关联的容器、虚拟机和虚拟机规模集。 它还允许在创建关联资源之前将 IP 地址作为后端池的一部分保留。 在[此处](backend-pool-management.md)了解详细信息|2021 年 3 月 |
| Feature | [标准 SKU 负载均衡器和公共 IP 的实例元数据支持](https://azure.microsoft.com/updates/standard-load-balancer-and-ip-addresses-metadata-now-available-through-azure-instance-metadata-service-imds/)|标准公共 IP 地址和标准负载均衡器的元数据现在可以通过 Azure 实例元数据服务 (IMDS) 进行检索。 元数据可从虚拟机 (VM) 的运行实例和虚拟机规模集 (VMSS) 实例中获得。 可以利用元数据来管理虚拟机。 在[此处](instance-metadata-service-load-balancer.md)了解详细信息| 2021 年 2 月 |
| Feature | [公共 IP SKU 从“基本”升级到“标准”，而不会丢失 IP 地址](https://azure.microsoft.com/updates/public-ip-sku-upgrade-generally-available/) | 从“基本”升级到“标准”负载均衡器时，请保留公共 IP 地址。 在[此处](../virtual-network/public-ip-upgrade-portal.md)了解详细信息| 2021 年 1 月|
| Feature | 支持跨资源组移动 | 标准负载均衡器和标准公共 IP 支持[资源组移动](https://azure.microsoft.com/updates/standard-resource-group-move/)。 | 2020 年 10 月 |
| Feature| 使用 Azure Monitor 的 Azure 负载均衡器见解 | 作为 Azure 网络监视器一部分，客户现在拥有所有负载平衡器配置的拓扑图，以及在 Azure 门户中预配置了指标的标准负载平衡器的运行状况仪表板。 [开始使用并了解详细信息](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020 年 6 月 |
| 验证 | 添加了 HA 端口验证 | 添加了验证，以确保仅当启用了浮动 IP 时才可配置 HA 端口规则和非 HA 端口规则。 之前此配置会执行，但不会按预期方式工作。 未对功能进行任何更改。 可在[此处](load-balancer-ha-ports-overview.md#limitations)了解详细信息| 2020 年 6 月 |
| Feature| 对 Azure 负载均衡器的 IPv6 支持（正式发布版） | 可以将 IPv6 地址作为 Azure 负载均衡器的前端。 了解如何[在此处创建双堆栈应用程序](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020 年 4 月|
| Feature| 空闲超时时的 TCP 重置（正式发布版）| 使用 TCP 重置创建更具可预测性的应用程序行为。 [了解详细信息](load-balancer-tcp-reset.md)| 2020 年 2 月 |

## <a name="known-issues"></a>已知问题

产品小组正在积极开发针对下列已知问题的解决方案：

|问题 |说明  |缓解措施  |
| ---------- |---------|---------|
| 基于 IP 的 LB 出站 IP | 未配置出站规则时，基于 IP 的 LB 利用 Azure 的默认出站访问 IP 进行出站 | 为了防止从此 IP 进行出站访问并防止 SNAT 端口耗尽，请利用出站规则或 NAT 网关获取可预测的 IP 地址 |

  

## <a name="next-steps"></a>后续步骤

有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](load-balancer-overview.md)和[常见问题解答](load-balancer-faqs.yml)。