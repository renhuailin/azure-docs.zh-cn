---
title: 已启用 Azure Arc 的 Kubernetes 概述
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 本文提供已启用 Azure Arc 的 Kubernetes 的概述。
keywords: Kubernetes、Arc、Azure、容器
ms.custom: references_regions
ms.openlocfilehash: b3296aa1c1d5d463cc7f4cb932b44ad76632e19e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121586"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>什么是已启用 Azure Arc 的 Kubernetes？

通过已启用 Azure Arc 的 Kubernetes，可在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集连接到 Azure Arc 时，它将：
* 通过 Azure 资源管理器 ID 和托管标识显示在 Azure 门户中。 
* 位于 Azure 订阅和资源组中。
* 接收标记，就像其他任何 Azure 资源一样。 

若要将 Kubernetes 群集连接到 Azure，群集管理员需要部署代理。 这些代理将执行以下操作：
* 作为标准 Kubernetes 部署在 `azure-arc` Kubernetes 命名空间中运行。
* 处理到 Azure 的连接。
* 收集 Azure Arc 日志和指标。
* 监视配置请求。 

已启用 Azure Arc 的 Kubernetes 支持行业标准 SSL 来保护传输中的数据。 此外，数据以静态加密方式存储在 Azure Cosmos DB 数据库中，确保数据保密性。

## <a name="supported-kubernetes-distributions"></a>支持的 Kubernetes 分布

已启用 Azure Arc 的 Kubernetes 适用于任何经云原生计算基础 (CNCF) 认证的 Kubernetes 群集。 Azure Arc 团队与[关键行业合作伙伴](./validation-program.md)合作，通过已启用 Azure Arc 的 Kubernetes 验证其 Kubernetes 分发的一致性。

## <a name="supported-scenarios"></a>支持的方案 

已启用 Azure Arc 的 Kubernetes 支持以下方案： 

* 连接在 Azure 外部运行的 Kubernetes，以便用于库存、分组和标记。

* 使用基于 GitOps 的配置管理来部署应用程序并应用配置。 

* 使用适用于容器的 Azure Monitor 来查看和监视群集。 

* 使用适用于 Kubernetes 的 Azure Policy 来应用策略。 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>支持的区域 

以下区域目前支持启用 Azure Arc 的 Kubernetes： 

* 美国东部
* 西欧
* 美国中西部
* 美国中南部
* 东南亚
* 英国南部
* 美国西部 2
* 澳大利亚东部
* 美国东部 2
* 北欧

## <a name="next-steps"></a>后续步骤

了解如何将群集连接到 Azure Arc。
> [!div class="nextstepaction"]
> [将群集连接到 Azure Arc](./quickstart-connect-cluster.md)
