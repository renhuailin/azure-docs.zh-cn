---
title: Open Service Mesh（预览版）
description: Azure Kubernetes 服务 (AKS) 中的 Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: cd1cfde1b3c1dbeda7fb112b29e8579f32bdf05a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439902"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS 附加产品（预览版）

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) 是一种轻型、可扩展的云原生服务网格，可让用户统一地管理、保护和获取高度动态微服务环境现成的可观测性功能。 

OSM 在 Kubernetes 上运行基于 Envoy 的控制平面，可以使用 [SMI](https://smi-spec.io/) API 进行配置，并通过将 Envoy 代理作为挎斗容器注入到每个应用程序实例旁边来运行。 Envoy 代理包含并执行有关访问控制策略的规则，实现路由配置，并捕获指标。 控制平面会持续配置代理，以确保策略和路由规则是最新的，并确保代理处于正常状态。

OSM 项目是由 Microsoft 发起的，此后接受了捐赠，并由[云原生计算基金会 (CNCF)](https://www.cncf.io/) 进行管理。 OSM 开放源代码项目将仍然是一个由社区主导的围绕特性和功能的协作项目，我们欢迎并鼓励大家对项目做出贡献。 有关参与方式，请参阅我们的[参与者阶梯](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md)指南。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>功能和特性

OSM 提供以下一组功能和特性，以便为 Azure Kubernetes 服务 (AKS) 群集提供云原生服务网格：

- OSM 已集成到 AKS 服务中，可提供完全受支持的托管服务网格体验，同时提供 AKS 附加功能赋予的便利性

- 通过启用 mTLS 来保护服务与服务之间的通信

- 通过启用 Envoy 挎斗代理自动注入，轻松地将应用程序加入到网格中

- 轻松透明地为在部署中转移的流量进行配置

- 能够为服务定义并执行细化的访问控制策略

- 提供对应用程序指标的观察和见解，用于调试和监视服务

- 通过可插入接口与外部证书管理服务/解决方案集成

## <a name="scenarios"></a>方案

OSM 可在以下方案中为 AKS 部署提供帮助：

- 在群集中部署的服务终结点之间提供加密通信

- 为网格中的 HTTP/HTTPS 流量和 TCP 流量授权

- 在 A/B 部署或 Canary 部署的两个或多个服务之间配置加权流量控制

- 从应用程序流量收集 KPI 和查看 KPI

## <a name="osm-service-quotas-and-limits-preview"></a>OSM 服务配额和限制（预览版）

可在 AKS [配额和区域限制页](./quotas-skus-regions.md)中找到 OSM 预览版的服务配额和限制。

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
