---
title: include 文件
description: include 文件
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: d9566564c168e37477d8a0a8238c93dfb8be054d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336181"
---
| 资源                                                                                                           | 限制                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 每个订阅的最大群集数                                                                                  | 5000                                                                                                                                                                                                        |
| 包含虚拟机可用性集和基本负载均衡器 SKU 的每个群集的最大节点数                       | 100                                                                                                                                                                                                         |
| 包含虚拟机规模集和[标准负载均衡器 SKU][standard-load-balancer] 的每个群集的最大节点数 | 1000（在所有[节点池][node-pool]中）                                            |
| 每个群集的最大节点池数                                                                                     | 100                                                                                  |
| 每个节点的最大 Pod 数：带 Kubenet 的[基本网络][basic-networking]                                           | 最大值：250 <br /> Azure CLI 默认值：110 <br /> Azure 资源管理器模板默认值：110 <br /> Azure 门户部署默认值：30          |
| 每个节点的最大 Pod 数：通过 Azure 容器联网界面进行[高级联网][advanced-networking]        | 最大值：250 <br /> 默认值：30                                                      |
| Open Service Mesh (OSM) AKS 加载项预览版                                                                          | Kubernetes 群集版本：1.19+<sup>1</sup><br />每个群集的 OSM 控制器数：1<sup>1</sup><br />每个 OSM 控制器的 Pod 数：500<sup>1</sup><br />由 OSM 管理的 Kubernetes 服务帐户数：50<sup>1</sup> |

<sup>1</sup>用于 AKS 的 OSM 加载项处于预览状态，在正式发布 (GA) 之前，还将会进行其他改进。 在预览阶段，建议不要超过显示的限制。<br />

> [!IMPORTANT]
> 建议客户为生产和性能/负载测试工作负载启用运行时间 SLA 层。 运行时间 SLA 层可实现 Kubernetes 控制平面的自动缩放，是为在[运行时间 SLA](../articles/aks/uptime-sla.md) 中具有生产工作负载的群集提供支持所必需的。

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
