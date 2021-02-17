---
title: 使用 Azure 策略大规模应用群集配置（预览版）
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure 策略大规模应用群集配置
keywords: Kubernetes、Arc、Azure、K8s、容器
ms.openlocfilehash: b80e50cb4823632f054de3b7f9da71392f8578d7
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560191"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>使用 Azure 策略大规模应用群集配置（预览版）

## <a name="overview"></a>概述

可以使用 Azure 策略强制执行以下任一资源，以实现特定 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 应用：
*  `Microsoft.Kubernetes/connectedclusters` 资源.
* 启用 GitOps 的 `Microsoft.ContainerService/managedClusters` 资源。 

若要使用 Azure 策略，请选择现有策略定义并创建策略分配。 创建策略分配时：
1. 设置分配的范围。
    * 作用域是 Azure 资源组或订阅。 
2. 设置要创建的的参数 `sourceControlConfiguration` 。 

创建分配之后，Azure 策略引擎会标识 `connectedCluster` `managedCluster` 位于范围内的所有或资源，并将其应用于 `sourceControlConfiguration` 每个资源。

可以通过使用多个策略分配，将多个 Git 存储库作为每个群集的事实源。 每个策略分配都将配置为使用不同的 Git 存储库;例如，一个存储库用于中心 IT/群集操作员，另一个用于应用程序团队的存储库。

## <a name="prerequisite"></a>先决条件

验证你在 `Microsoft.Authorization/policyAssignments/write` 将创建此策略分配)  (订阅或资源组的作用域的权限。

## <a name="create-a-policy-assignment"></a>创建策略分配

1. 在 Azure 门户中，导航到 " **策略**"。
1. 在边栏的 " **创作** " 部分，选择 " **定义**"。
1. 在 "Kubernetes" 类别中，选择 "将 GitOps 部署到 Kubernetes 群集" 内置策略。 
1. 单击 **分配**。
1. 将 **作用域** 设置为将应用策略分配的管理组、订阅或资源组。
    * 如果要从策略作用域中排除任何资源，请设置 " **排除**"。
1. 为策略分配指定易于识别的 **名称** 和 **描述**。
1. 确保 " **策略强制** " 设置为 " **已启用**"。
1. 选择“下一步”。
1. 设置要在创建时使用的参数值 `sourceControlConfiguration` 。
1. 选择“**下一页**”。
1. 启用“创建修正任务”。
1. 验证 " **创建托管标识** " 已选中，并且该标识将具有 **参与者** 权限。 
    * 有关详细信息，请参阅 [创建策略分配快速入门](../../governance/policy/assign-policy-portal.md) 和 [使用 Azure 策略更正不符合资源一文](../../governance/policy/how-to/remediate-resources.md)。
1. 选择“查看 + 创建”。

创建策略分配后， `sourceControlConfiguration` 将对在分配范围内的以下任何资源应用：
* 新 `connectedCluster` 资源。
* `managedCluster`已安装 GitOps 代理的新资源。 

对于现有群集，需要手动运行修正任务。 此任务通常需要10到20分钟才能生效。

## <a name="verify-a-policy-assignment"></a>验证策略分配

1. 在 Azure 门户中，导航到某个 `connectedCluster` 资源。
1. 在边栏的 " **设置** " 部分中，选择 " **策略**"。 
    * 尚未实现 AKS 群集 UX。
    * 在策略列表中，应会看到之前创建的策略分配，并将 **符合性状态** 设置为 " *符合*"。
1. 在边栏的“设置”部分中，选择“配置” 。
    * 在 "配置" 列表中，应会看到已 `sourceControlConfiguration` 创建的策略分配。
1. 使用 `kubectl` 来询问群集。 
    * 应会看到命名空间和创建的项目 `sourceControlConfiguration` 。
    * 在 5 分钟内，你应会在群集中看到在所配置 Git 存储库的清单中描述的项目。

## <a name="next-steps"></a>后续步骤

* [使用已启用 Arc 的 Kubernetes 群集设置适用于容器的 Azure Monitor](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
