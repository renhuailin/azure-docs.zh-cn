---
title: 托管 Kubernetes 客户端应用程序的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何托管使用 Azure Cache for Redis 的 Kubernetes 客户端应用程序。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 5d61b7047262c86471dba988bd0f80cb4fdac8ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663623"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes 托管的客户端应用程序

## <a name="multiple-pods"></a>多个 Pod

如果有多个 Pod 连接到 Redis 服务器，请确保以交错方式创建来自 Pod 的新连接。 如果多个 Pod 在短时间内未交错启动，则会导致创建的客户端连接数突然激增。 连接数较高会导致 Redis 服务器上的负载较高，并可能导致超时。

在同时关闭多个 Pod 时，避免出现此相同情况。 未能以交错方式关闭可能会导致连接数急剧下降，从而造成 CPU 压力。

## <a name="sufficient-resources"></a>足够的资源

确保托管连接到 Redis 服务器的 Pod 的 Kubernetes 节点具有足够的内存、CPU 和网络带宽。  

## <a name="noisy-neighbor-problem"></a>近邻干扰问题

当心近邻干扰问题。 运行客户端的 Pod 可能会受同一节点上运行的其他 Pod 的影响，并限制 Redis 连接或 IO 操作。

## <a name="next-steps"></a>后续步骤

- [开发](cache-best-practices-development.md)
- [Azure Cache for Redis 开发的常见问题解答](cache-development-faq.yml)
