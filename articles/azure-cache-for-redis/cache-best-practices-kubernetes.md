---
title: 托管 Kubernetes 客户端应用程序的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何托管使用 Azure Cache for Redis 的 Kubernetes 客户端应用程序。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 27117f8839c6ea0d98c2f15e01ee6a3ab3ec36c6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113589"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes 托管的客户端应用程序

## <a name="multiple-pods"></a>多个 Pod

如果有多个 Pod 连接到 Redis 服务器，请确保以交错方式创建来自 Pod 的新连接。 如果多个 Pod 在短时间内未交错启动，则会导致创建的客户端连接数突然激增。 连接数较高会导致 Redis 服务器上的负载较高，并可能导致超时。

在同时关闭多个 Pod 时，避免出现此相同情况。 未能以交错方式关闭可能会导致连接数急剧下降，从而造成 CPU 压力。

## <a name="sufficient-resources"></a>足够的资源

确保托管连接到 Redis 服务器的 Pod 的 Kubernetes 节点具有足够的内存、CPU 和网络带宽。  

## <a name="noisy-neighbor-problem"></a>近邻干扰问题

当心近邻干扰问题。 运行客户端的 Pod 可能会受同一节点上运行的其他 Pod 的影响，并限制 Redis 连接或 IO 操作。
