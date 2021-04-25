---
title: 如何规划和部署已启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 将大量群集加入已启用 Azure Arc 的 Kubernetes 以便进行配置管理
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315146"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>规划和部署已启用 Azure Arc 的 Kubernetes

对于任何一家公司而言，部署 IT 基础结构服务或业务应用程序都充满了挑战。 若要防止任何意外变故或计划外成本，需要进行全面的规划，确保做好尽量充分的准备。 此类计划应确定完成任务需要满足的设计和部署条件。

要使部署顺利继续，应在计划中明确以下方面的认知：

* 角色和职责。
* 所有 Kubernetes 群集的清单
* 满足网络要求。
* 实现成功部署和日常管理所需的技能集与培训。
* 验收条件以及如何跟踪其成功结果。
* 用于自动化部署的工具或方法。
* 已确定的风险和缓解计划，旨在避免延误和中断。
* 在部署期间如何避免中断。
* 出现重大问题时的事务升级路径是什么？

本文旨在确保你充分准备好在环境中的多个生产群集间成功部署已启用 Azure Arc 的 Kubernetes。

## <a name="prerequisites"></a>先决条件

* 一个现有 Kubernetes 群集。 如果没有群集，你可以使用以下任意选项创建群集：
    - [Docker 中的 Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    - 使用 [Docker for Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 或 [Docker for Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 创建 Kubernetes 群集
    - 使用[群集 API](https://cluster-api.sigs.k8s.io/user/quick-start.html) 的自托管 Kubernetes 群集

* 计算机可以直接或者通过代理服务器从本地网络或其他云环境连接到 Azure 中的资源。 可以在[网络先决条件](quickstart-connect-cluster.md#meet-network-requirements)下找到更多详细信息。

* 一个 `kubeconfig` 文件，指向要连接到 Azure Arc 的群集。
* 创建已启用 Azure Arc 的 Kubernetes 资源类型 `Microsoft.Kubernetes/connectedClusters` 的用户或服务主体的“读取”和“写入”权限。

## <a name="pilot"></a>试点

在部署到所有生产群集之前，请先评估此部署过程，然后再在环境中广泛采用这种部署。 要进行试点，请确定几台有代表性的、对公司业务运营不很关键的群集。 请务必留出足够的时间来运行试点并评估其影响：建议留出大约 30 天时间。

制定正式的计划用于描述试点范围和细节。 下面的示例计划应可帮助你开始进行。

* 目标 - 描述哪些业务和技术驱动因素导致你认定试点是有必要的。
* 选择条件 - 指定通过试点展示解决方案的哪些方面时所要依据的条件。
* 范围 - 涵盖解决方案组件、预期计划、试点持续时间和目标群集数。
* 成功条件和指标 - 定义试点的成功条件，以及用于确定成功程度的具体措施。
* 培训计划 - 描述在试点期间对 Azure 及其服务不太熟悉的系统工程师、管理员等人员的培训计划。
* 过渡计划 - 描述用于引导从试点环境过渡到生产环境的策略和条件。
* 回滚 - 描述从试点回滚到部署前状态的过程。
* 风险 - 列出所有已识别到的，与开展试点以及与生产部署相关的风险。

## <a name="phase-1-build-a-foundation"></a>第 1 阶段：构建基础

在此阶段中，系统工程师或管理员执行核心活动，例如创建资源组、标记和角色分配，以便随后可以创建和操作已启用 Azure Arc 的 Kubernetes 资源。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
| [创建资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | 一个专用的资源组，仅包含已启用 Azure Arc 的 Kubernetes 资源，并对这些资源进行集中式管理和监视。 | 一小时 |
| 应用[标记](../../azure-resource-manager/management/tag-resources.md)来帮助对计算机进行组织。 | 评估和制定 IT 协调的[标记策略](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)。 这可以帮助降低管理已启用 Azure Arc 的 Kubernetes 资源的复杂性，并简化管理决策制定。 | 一天 |
| 确定 GitOps 的[配置](tutorial-use-gitops-connected-cluster.md) | 确定要部署到群集的应用程序或基线配置（如 `PodSecurityPolicy`、`NetworkPolicy`） | 一天 |
| [制定 Azure Policy](../../governance/policy/overview.md) 监管计划 | 确定如何使用 Azure Policy 在订阅或资源组范围实施已启用 Azure Arc 的 Kubernetes 群集的治理。 | 一天 |
| 配置[基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC) | 制定访问计划，以确定对群集拥有读取/写入/所有权限的人员 | 一天 |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>第 2 阶段：规划和部署已启用 Azure Arc 的 Kubernetes

在此阶段，我们会将 Kubernetes 群集连接到 Azure：

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
| [将第一个 Kubernetes 群集连接到 Azure Arc](quickstart-connect-cluster.md) | 在将第一个群集连接到 Azure 的过程中需设置加入环境，其中包含所有必需工具，如 Azure CLI、Helm 以及适用于 Azure CLI 的 `connectedk8s` 扩展。 | 15 分钟 |
| [创建服务主体](create-onboarding-service-principal.md) | 创建一个服务主体，以使用 Azure CLI 或 PowerShell 以非交互方式连接 Kubernetes 群集。 | 一小时 |


## <a name="phase-3-manage-and-operate"></a>第 3 阶段：管理和操作

在此阶段，我们将应用程序和基线配置部署到 Kubernetes 群集。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
|在群集上[创建配置](tutorial-use-gitops-connected-cluster.md) | 创建配置以在已启用 Azure Arc 的 Kubernetes 资源上部署应用程序。 | 15 分钟 |
|[使用 Azure Policy](use-azure-policy.md) 大规模强制执行配置 | 创建策略分配，以便在订阅或资源组范围内的所有群集中自动部署基线配置。 | 15 分钟 |
| [升级 Azure Arc 代理](agent-upgrade.md) | 如果在群集上禁用了代理的自动升级，请手动将代理更新到最新版本，以确保具有最新的安全性和 bug 修补程序。 | 15 分钟 |

## <a name="next-steps"></a>后续步骤

* 使用我们的快速入门[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 在已启用 Azure Arc 的 Kubernetes 群集上[创建配置](./tutorial-use-gitops-connected-cluster.md)。
* [使用 Azure Policy 大规模应用配置](./use-azure-policy.md)。