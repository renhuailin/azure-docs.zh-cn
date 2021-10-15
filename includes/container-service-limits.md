---
title: include 文件
description: include 文件
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 17a98a50f4c6e6d0723b62f1ea402c78f653bf4c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129377886"
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

<sup>1</sup> 用于 AKS 的 OSM 加载项处于预览状态，在正式发布 (GA) 之前，还会进行其他改进。 在预览阶段，建议不要超过显示的限制。<br />

<br />

| Kubernetes 控制平面层 | 限制 |  
| -------------- | :--------------------------------------------- |
| 付费层      | 根据负载自动横向扩展     |
| 免费层      | 资源有限，[即时请求限制](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)为 50 个变动调用和 100 个只读调用   |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
