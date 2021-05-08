---
title: 带有容器见解的部署和 HPA 指标| Microsoft Docs
description: 本文介绍用容器见解收集的部署和 HPA（水平 pod 自动缩放器）指标。
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717700"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>带有容器见解的部署和 HPA 指标

从代理版本 ciprod08072020 开始，集成了容器见解的代理现在可以收集部署和 HPA 指标。

## <a name="deployment-metrics"></a>部署指标

通过以 60 秒的间隔收集以下指标并将其存储在 InsightMetric 表中，容器见解会自动开始监视部署：

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status.updatedReplicas) | 此部署所针对的就绪 Pod 总数 (status.readyReplicas)。 以下是此指标的维度。 <ul> <li> 部署 - 部署的名称 </li> <li> k8sNamespace - 用于部署的 Kubernetes 命名空间 </li> <li> deploymentStrategy - 用于将 Pod 替换为新 Pod 的部署策略 (spec.strategy.type)</li><li> creationTime - 部署创建时间戳 </li> <li> spec_replicas - 所需 Pod 数 (spec.replicas) </li> <li>status_replicas_available - 此部署针对的可用 Pod 总数（准备至少 minReadySeconds）(status.availableReplicas)</li><li>status_replicas_updated - 此部署针对的具有所需模板规范的未终止 Pod 的总数 (status.updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA 指标

通过以 60 秒的间隔收集以下指标并将其存储在 InsightMetric 表中，容器见解会自动开始监视 HPA：

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hpa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | 此自动缩放器管理的 Pod 的当前副本数 (status.currentReplicas)。 以下是此指标的维度。 <ul> <li> hpa - HPA 的名称 </li> <li> k8sNamespace - 用于 HPA 的 Kubernetes 命名空间 </li> <li> lastScaleTime - HPA 上一次扩展 Pod 数 (status.lastScaleTime)</li><li> creationTime - HPA 创建时间戳 </li> <li> spec_max_replicas - 自动缩放器可以设置的 Pod 数量上限 (spec.maxReplicas) </li> <li> spec_min_replicas - 自动缩放器可以纵向缩减到的副本数的下限 (spec.minReplicas) </li><li>status_desired_replicas - 此自动缩放器管理的所需 Pod 副本数 (status.desiredReplicas)</li><li>targetKind - HPA 的目标种类 (spec.scaleTargetRef.kind) </li><li>targetName - HPA 的目标名称 (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>部署和 HPA 图表 

容器见解包含表中前面列出的指标的预配置图表，作为每个群集的工作簿。 可以直接从 AKS 群集中找到部署和 HPA 工作簿“部署和 HPA”，方法是从左侧窗格中选择“工作簿”，然后从见解中的“查看工作簿”下拉列表中找到  。

## <a name="next-steps"></a>后续步骤

- 查看 [Kubernetes 中的 Kube 状态指标](https://github.com/kubernetes/kube-state-metrics/tree/master/docs)，了解有关 Kube 状态指标的详细信息。