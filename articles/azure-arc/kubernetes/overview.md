---
title: 已启用 Azure Arc 的 Kubernetes 概述
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 本文提供已启用 Azure Arc 的 Kubernetes 的概述。
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: 80d1b741c4a3a1d60cc44e3afdeac22a61a7db4b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768281"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>什么是已启用 Azure Arc 的 Kubernetes？

通过已启用 Azure Arc 的 Kubernetes，可在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集连接到 Azure Arc 时，它将：
* 通过 Azure 资源管理器 ID 和托管标识显示在 Azure 门户中。 
* 被置于 Azure 订阅和资源组中。
* 接收标记，就像其他任何 Azure 资源一样。 

若要将 Kubernetes 群集连接到 Azure，群集管理员需要部署代理。 这些代理将执行以下操作：
* 作为标准 Kubernetes 部署在 `azure-arc` Kubernetes 命名空间中运行。
* 处理到 Azure 的连接。
* 收集 Azure Arc 日志和指标。
* 监视配置请求。 

已启用 Azure Arc 的 Kubernetes 支持行业标准 SSL 来保护传输中的数据。 此外，静态数据将以加密方式存储在 Azure Cosmos DB 数据库中，以确保数据机密性。

## <a name="supported-kubernetes-distributions"></a>支持的 Kubernetes 分布

已启用 Azure Arc 的 Kubernetes 适用于任何经云原生计算基础 (CNCF) 认证的 Kubernetes 群集。 Azure Arc 团队与[关键行业合作伙伴](./validation-program.md)合作，通过已启用 Azure Arc 的 Kubernetes 验证其 Kubernetes 分发的一致性。

## <a name="supported-scenarios"></a>支持的方案 

已启用 Azure Arc 的 Kubernetes 支持以下方案： 

* 连接在 Azure 外部运行的 Kubernetes，以便用于库存、分组和标记。

* 使用基于 GitOps 的配置管理来部署应用程序并应用配置。 

* 使用适用于容器的 Azure Monitor 来查看和监视群集。

* 使用 Azure Defender for Kubernetes 强制实施威胁防护。

* 使用适用于 Kubernetes 的 Azure Policy 来应用策略定义。

* 创建[自定义位置](./custom-locations.md)作为目标位置，用于部署已启用 Azure Arc 的数据服务、[Azure Arc 上的应用服务](../../app-service/overview-arc-integration.md)（包括 Web、函数和逻辑应用）和 [Kubernetes 上的事件网格](../../event-grid/kubernetes/overview.md)。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="next-steps"></a>后续步骤

了解如何将群集连接到 Azure Arc。
> [!div class="nextstepaction"]
> [将群集连接到 Azure Arc](./quickstart-connect-cluster.md)