---
title: 缩放 Azure Cache for Redis 的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何缩放 Azure Cache for Redis。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 01fbebb06ba408232add1f0f05c5b715816e9e69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113552"
---
# <a name="scaling"></a>扩展

## <a name="scaling-under-load"></a>在负载较低的情况下进行缩放

在负载较低的情况下缩放缓存时，请配置 maxmemory-reserved 设置来提高系统响应能力。 有关详细信息，请参阅[配置 maxmemory-reserved 设置](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)。

## <a name="scaling-clusters"></a>缩放群集

在横向扩展或缩减群集缓存之前，请尝试尽可能减少缓存中的数据。减少数据可确保必须移动的数据量较小，从而减少缩放操作所需的时间。 有关何时缩放的详细信息，请参阅[何时缩放](cache-how-to-scale.md#when-to-scale)。

## <a name="scale-before-load-is-too-high"></a>在负载过高前进行缩放

在服务器负载或内存使用量过高之前开始缩放。 如果过高，则意味着 Redis 服务器正忙。 处于繁忙状态的 Redis 服务器没有足够的资源来缩放和重新分发数据。

## <a name="cache-sizes"></a>缓存大小

如果使用 TLS 且连接数量很多，请考虑横向扩展，以便可以将负载分散到更多核心上。 某些大小的缓存托管在具有 4 个或更多核心的 VM 上。

将 TLS 加密/解密以及 TLS 连接/断开连接工作负载分散到多个核心，使缓存 VM 上的总体 CPU 使用率降低。 有关详细信息，请参阅[有关 VM 大小和核心数的详细信息](./cache-planning-faq.yml#azure-cache-for-redis-performance)。
