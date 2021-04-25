---
title: 使用 Azure 策略大规模应用群集配置
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure 策略大规模应用群集配置
keywords: Kubernetes、Arc、Azure、K8s、容器
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121450"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>使用 Azure 策略大规模应用群集配置

可以使用 Azure Policy 大规模地对已启用 Azure Arc 的 Kubernetes 群集 (`Microsoft.Kubernetes/connectedclusters`) 应用配置（`Microsoft.KubernetesConfiguration/sourceControlConfigurations` 资源类型）。

若要使用 Azure Policy，请选择现有策略定义并创建策略分配。 创建策略分配时：
1. 设置分配的范围。
    * 范围是 Azure 资源组或订阅。 
2. 为将创建的配置设置参数。 

创建分配后，Azure Policy 引擎会标识范围内所有已启用 Azure Arc 的 Kubernetes 群集，并将配置应用到每个群集。

可以创建多个配置，每个配置都指向不同的 Git 存储库，并使用多个策略分配。 例如，一个存储库用于中心 IT/群集操作员，另一个存储库用于应用程序团队。

## <a name="prerequisite"></a>先决条件

验证你对要创建此策略分配的范围（订阅或资源组）具有 `Microsoft.Authorization/policyAssignments/write` 权限。

## <a name="create-a-policy-assignment"></a>创建策略分配

1. 在 Azure 门户中，导航到“策略”。
1. 在边栏的“创作”部分，选择“定义” 。
1. 在“Kubernetes”类别中，选择“将 GitOps 部署到 Kubernetes 群集”内置策略。 
1. 单击 **分配**。
1. 将“范围”设置为将应用策略分配的管理组、订阅或资源组。
    * 如果要从策略范围中排除任何资源，请设置“排除”。
1. 为策略分配指定易于识别的“名称”和“描述” 。
1. 确保“策略实施”设置为“已启用” 。
1. 选择“下一步”。
1. 设置要在创建 `sourceControlConfiguration` 时使用的参数值。
1. 选择“**下一页**”。
1. 启用“创建修正任务”。
1. 验证已选中“创建托管标识”，并确保标识具有“参与者”权限 。 
    * 有关详细信息，请参阅[创建策略分配快速入门](../../governance/policy/assign-policy-portal.md)和[使用 Azure Policy 修正不符合条件的资源](../../governance/policy/how-to/remediate-resources.md)文章。
1. 选择“查看 + 创建”。

创建策略分配后，配置将应用于在策略分配范围内新创建的已启用 Azure Arc 的 Kubernetes 群集。

对于现有群集，需要手动运行修正任务。 此任务通常需要 10 到 20 分钟，策略分配才能生效。

## <a name="verify-a-policy-assignment"></a>验证策略分配

1. 在 Azure 门户中，导航到已启用 Azure Arc 的 Kubernetes 群集之一。
1. 在边栏的“设置”部分中，选择“策略” 。 
    * 在策略列表中，应会看到之前创建的策略分配，并且“符合性状态”应设为“符合”。
1. 在边栏的“设置”部分中，选择“配置” 。
    * 在配置列表中，应会看到策略分配创建的配置。
1. 使用 `kubectl` 来询问群集。 
    * 应会看到配置资源创建的命名空间和项目。
    * 在 5 分钟内（假设群集已通过网络连接到 Azure），应会看到 Git 存储库中的清单描述的对象，这些对象是在集群上创建的。

## <a name="next-steps"></a>后续步骤

[使用已启用 Azure Arc 的 Kubernetes 群集设置适用于容器的 Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)。
