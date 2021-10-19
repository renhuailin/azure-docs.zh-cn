---
title: 托管 Kubernetes 客户端应用程序的最佳做法
titleSuffix: Azure Cache for Redis
description: 了解如何托管使用 Azure Cache for Redis 的 Kubernetes 客户端应用程序。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 33628fc16dfc2693cad0f8cd13f555cc10e55f25
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808743"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes 托管的客户端应用程序

## <a name="client-connections-from-multiple-pods"></a>来自多个 pod 的客户端连接

如果有多个 Pod 连接到 Redis 服务器，请确保以交错方式创建来自 Pod 的新连接。 如果多个 Pod 在短时间内未交错启动，则会导致创建的客户端连接数突然激增。 连接数较高会导致 Redis 服务器上的负载较高，并可能导致超时。

在同时关闭多个 Pod 时，避免出现此相同情况。 未能以交错方式关闭可能会导致连接数急剧下降，从而造成 CPU 压力。

## <a name="sufficient-pod-resources"></a>足够的 Pod 资源

请确保有足够的 CPU 和内存资源支持 Pod 运行客户端应用程序。 如果客户端应用程序的运行接近其资源限制，则可能会导致超时。

## <a name="sufficient-node-resources"></a>足够的节点资源

运行客户端应用程序的 Pod 可能会受同一节点上运行的其他 Pod 的影响，并限制 Redis 连接或 IO 操作。 因此，请务必确保客户端应用程序 Pod 在其上运行的节点具有足够的内存、CPU 和网络带宽。 在上述任何不足量的资源上运行可能会导致连接问题。

## <a name="linux-hosted-client-applications-and-tcp-settings"></a>Linux 托管客户端应用程序与 TCP 设置

如果 Azure Cache for Redis 客户端应用程序在基于 Linux 的容器上运行，我们建议更新某些 TCP 设置，如 [Linux 托管客户端应用程序的 TCP 设置](cache-best-practices-connection.md#tcp-settings-for-linux-hosted-client-applications)中所述。

## <a name="next-steps"></a>后续步骤

- [开发](cache-best-practices-development.md)
- [Azure Cache for Redis 开发的常见问题解答](cache-development-faq.yml)
