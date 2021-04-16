---
title: include 文件
description: include 文件
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081007"
---
| 资源                                                                                                           | 限制                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 每个订阅的最大群集数                                                                                  | 1000                                                                                                                                                                                                        |
| 包含虚拟机可用性集和基本负载均衡器 SKU 的每个群集的最大节点数                       | 100                                                                                                                                                                                                         |
| 包含虚拟机规模集和[标准负载均衡器 SKU][standard-load-balancer] 的每个群集的最大节点数 | 1000（每个[节点池][node-pool] 100 个节点）                                                                                                                                                                 |
| 每个节点的最大 Pod 数：带 Kubenet 的[基本网络][basic-networking]                                           | 110                                                                                                                                                                                                         |
| 每个节点的最大 Pod 数：通过 Azure 容器联网界面进行[高级联网][advanced-networking]        | Azure CLI 部署：30<sup>1</sup><br />Azure 资源管理器模板：30<sup>1</sup><br />门户部署：30                                                                                        |
| Open Service Mesh (OSM) AKS 加载项预览版                                                                          | Kubernetes 群集版本：1.19+<sup>2</sup><br />每个群集的 OSM 控制器数：1<sup>2</sup><br />每个 OSM 控制器的 Pod 数：500<sup>2</sup><br />由 OSM 管理的 Kubernetes 服务帐户数：50<sup>2</sup> |

<sup>1</sup>使用 Azure CLI 或资源管理器模板部署 Azure Kubernetes 服务 (AKS) 群集时，此值是可以配置的，最大可以配置为每节点 250 个 Pod。 在部署 AKS 群集以后，或者在使用 Azure 门户部署群集的情况下，不能配置每节点的最大 Pod 数。<br />

<sup>2</sup>用于 AKS 的 OSM 加载项处于预览状态，在正式发布 (GA) 之前，还将会进行其他改进。 在预览阶段，建议不要超过显示的限制。<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
