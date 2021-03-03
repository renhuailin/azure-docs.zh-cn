---
title: 使用 Azure 策略大规模应用群集配置
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure 策略大规模应用群集配置
keywords: Kubernetes、Arc、Azure、K8s、容器
ms.openlocfilehash: 7f85050666c383ba49730bd88ce1f26d55607e7a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652141"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>使用 Azure 策略大规模应用群集配置

## <a name="overview"></a>概述

可以使用 Azure 策略将配置 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations` 资源类型) 大规模应用于启用了 Azure Arc 的 Kubernetes 群集 (`Microsoft.Kubernetes/connectedclusters`) 。

若要使用 Azure 策略，请选择现有策略定义并创建策略分配。 创建策略分配时：
1. 设置分配的范围。
    * 作用域是 Azure 资源组或订阅。 
2. 设置要创建的配置的参数。 

创建分配后，Azure 策略引擎会标识位于范围内的所有启用了 Azure Arc 的 Kubernetes 群集，并将配置应用到每个群集。

可以创建多个配置，每个配置都指向不同的 Git 存储库，并使用多个策略分配。 例如，一个存储库用于中心 IT/群集操作员，另一个存储库用于应用程序团队。

## <a name="prerequisite"></a>先决条件

验证你是否拥有 `Microsoft.Authorization/policyAssignments/write` (订阅或资源组的作用域的权限) 你将在此创建此策略分配。

## <a name="create-a-policy-assignment"></a>创建策略分配

1. 在 Azure 门户中，导航到 " **策略**"。
1. 在边栏的 " **创作** " 部分，选择 " **定义**"。
1. 在 "Kubernetes" 类别中，选择 "将 GitOps 部署到 Kubernetes 群集" 内置策略。 
1. 单击 **分配**。
1. 将 **作用域** 设置为将应用策略分配的管理组、订阅或资源组。
    * 如果要从策略作用域中排除任何资源，请设置 " **排除**"。
1. 为策略分配指定易于识别的 **名称** 和 **描述**。
1. 确保 " **策略强制** " 设置为 " **已启用**"。
1. 选择“**下一页**”。
1. 设置要在创建时使用的参数值 `sourceControlConfiguration` 。
1. 选择“**下一页**”。
1. 启用“创建修正任务”。
1. 验证 " **创建托管标识** " 已选中，并且该标识将具有 **参与者** 权限。 
    * 有关详细信息，请参阅 [创建策略分配快速入门](../../governance/policy/assign-policy-portal.md) 和 [使用 Azure 策略更正不符合资源一文](../../governance/policy/how-to/remediate-resources.md)。
1. 选择“查看 + 创建”。

创建策略分配后，配置将应用于在策略分配范围内创建的新的启用 Azure Arc Kubernetes 群集。

对于现有群集，你将需要手动运行补救任务。 此任务通常需要10到20分钟才能生效。

## <a name="verify-a-policy-assignment"></a>验证策略分配

1. 在 Azure 门户中，导航到已启用 Azure Arc 的 Kubernetes 群集之一。
1. 在边栏的 " **设置** " 部分中，选择 " **策略**"。 
    * 在策略列表中，应会看到之前创建的策略分配，并将 **符合性状态** 设置为 " *符合*"。
1. 在边栏的“设置”部分中，选择“配置” 。
    * 在 "配置" 列表中，应会看到策略分配创建的配置。
1. 使用 `kubectl` 来询问群集。 
    * 应会看到配置资源创建的命名空间和项目。
    * 在5分钟内 (假设群集已通过网络连接到 Azure) ，你应看到 Git 存储库中的清单描述的对象，在群集上创建。

## <a name="next-steps"></a>后续步骤

* [使用已启用 Arc 的 Kubernetes 群集设置适用于容器的 Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)
