---
title: 已启用 Azure Arc 的 Kubernetes 代理体系结构
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文提供已启用 Azure Arc 的 Kubernetes 代理的体系结构概述
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: ec95efdfef871777e7f53617b057529e301739dd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953062"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>已启用 Azure Arc 的 Kubernetes 代理体系结构

[Kubernetes](https://kubernetes.io/) 本身可以在混合环境和多云环境中以一致的方式部署容器化工作负载。 但是，已启用 Azure Arc 的 Kubernetes 可充当集中式一致控制平面，从而跨异构环境管理策略、调控和安全性。 本文提供以下内容：

* 将群集连接到 Azure Arc 的体系结构概述。
* 代理遵循的连接模式。
* 群集环境与 Azure 之间交换的数据的说明。

## <a name="deploy-agents-to-your-cluster"></a>将代理部署到群集

大多数本地数据中心强制实施严格的网络规则，防止在网络边界防火墙上进行入站通信。 已启用 Azure Arc 的 Kubernetes 不需要使用防火墙上的入站端口，并且只启用选择性出口终结点用于出站通信，因此可以在这些限制下正常工作。 已启用 Azure Arc 的 Kubernetes 代理发起此出站通信。 

![体系结构概述](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>将群集连接到 Azure Arc

1. 在你选择的基础结构（VMware vSphere、Amazon Web Services、Google Cloud Platform 等）上创建 Kubernetes 群集。 

    > [!NOTE]
    > 由于已启用 Azure Arc 的 Kubernetes 目前仅支持将现有的 Kubernetes 群集附加到 Azure Arc，因此客户需要自行创建并管理 Kubernetes 群集的生命周期。  

1. 使用 Azure CLI 开始为群集注册 Azure Arc。
    * Azure CLI 使用 Helm 在群集上部署代理 Helm 图表。
    * 群集节点向 [Microsoft 容器注册表](https://github.com/microsoft/containerregistry)发起出站通信，并拉取所需的映像用于在 `azure-arc` 命名空间中创建以下代理：

        | Agent | 说明 |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | 已启用 Azure Arc 的 Kubernetes 目前仅支持[系统分配的标识](../../active-directory/managed-identities-azure-resources/overview.md)。 `clusteridentityoperator` 发起首次出站通信。 此首次通信将提取由其他代理用来与 Azure 通信的托管服务标识 (MSI) 证书。 |
        | `deployment.apps/config-agent` | 监视连接的群集上应用的源代码管理配置资源。 更新符合性状态。 |
        | `deployment.apps/controller-manager` | 用于协调 Azure Arc 组件之间的交互的一个或多个 operator。 |    
        | `deployment.apps/metrics-agent` | 收集其他 Arc 代理的指标以验证性能是否最佳。 |
        | `deployment.apps/cluster-metadata-operator` | 收集群集元数据，包括群集版本、节点计数和 Azure Arc 代理版本。 |
        | `deployment.apps/resource-sync-agent` | 将上述群集元数据同步到 Azure。 |
        | `deployment.apps/flux-logs-agent` | 从在源代码管理配置过程中部署的 flux operator 收集日志。 |
    
1. 在所有已启用 Azure Arc 的 Kubernetes 代理 Pod 均处于 `Running` 状态后，验证群集是否已连接到 Azure Arc。应会看到：
    * [Azure 资源管理器](../../azure-resource-manager/management/overview.md)中已启用 Azure Arc 的 Kubernetes 资源。 Azure 将此资源作为客户管理的 Kubernetes 群集的投影，而不是实际 Kubernetes 群集本身进行跟踪。
    * 群集元数据（例如 Kubernetes 版本、代理版本和节点数）以元数据的形式显示在已启用 Azure Arc 的 Kubernetes 资源上。

## <a name="data-exchange-between-cluster-environment-and-azure"></a>群集环境与 Azure 之间的数据交换

| 数据类型 | 方案 | 通信模式 |
| --------- | -------- | ------------------ |
| Kubernetes 群集版本 | 群集元数据 | 代理推送到 Azure |
| 群集中的节点数 | 群集元数据 | 代理推送到 Azure |
| 代理版本 | 群集元数据 | 代理推送到 Azure |
| Kubernetes 分发类型 | 群集元数据 | Azure CLI 推送到 Azure |
| 基础结构类型 (AWS/GCP/vSphere/...) | 群集元数据 | Azure CLI 推送到 Azure |
| 群集中节点的 vCPU 计数 | 计费 | Azure CLI 推送到 Azure |
| 代理检测信号 | 资源运行状况 | 代理推送到 Azure |
| 各代理的资源消耗量（内存/CPU） | 诊断和可支持性 | 代理推送到 Azure |
| 所有代理容器的日志 | 诊断和可支持性 | 代理推送到 Azure |
| 代理升级可用性 | 代理升级 | 代理从 Azure 拉取 |
| 配置的所需状态：Git 存储库 URL、flux operator 参数、私钥、已知 hosts 内容、HTTPS 用户名、令牌或密码 | 配置 | 代理从 Azure 拉取 |
| flux operator 安装状态 | 配置 | 代理推送到 Azure |
| 需要在群集内部强制实施 Gatekeeper 的 Azure Policy 分配 | Azure Policy | 代理从 Azure 拉取 |
| 群集内部策略强制实施的审核与符合性状态 | Azure Policy | 代理推送到 Azure |
| 客户工作负载的指标和日志 | Azure Monitor | 代理推送到客户租户和订阅中的 Log Analytics 工作区资源 |

## <a name="connectivity-status"></a>连接性状态

| 状态 | 说明 |
| ------ | ----------- |
| Connecting | 已启用 Azure Arc 的 Kubernetes 资源是在 Azure 资源管理器中创建的，但服务尚未收到代理检测信号。 |
| 已连接 | 已启用 Azure Arc 的 Kubernetes 服务在过去 15 分钟内的某个时间收到了代理检测信号。 |
| 脱机 | 已启用 Azure Arc 的 Kubernetes 资源事先已连接，但服务有 15 分钟未收到任何代理检测信号。 |
| 已过期 | MSI 证书过期时限为 90 天（在其颁发后）。 此证书过期后，会将资源视为 `Expired`，所有功能（例如配置、监视和策略）将在此群集上停止工作。 在[常见问题解答文章](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)中可以找到有关如何解决已启用 Azure Arc 的 Kubernetes 资源过期问题的详细信息。 |

## <a name="understand-connectivity-modes"></a>了解连接模式

| 连接模式 | 说明 |
| ----------------- | ----------- |
| 完全连接 | 代理能够平稳地与 Azure 通信，只是在传播 GitOps 配置、强制实施 Azure Policy 和 Gatekeeper 策略以及在 Azure Monitor 中收集工作负载指标和日志时稍有延迟。 |
| 半连接 | `clusteridentityoperator` 拉取的 MSI 证书的有效期最长为 90天。 过期后，已启用 Azure Arc 的 Kubernetes 资源将停止工作。 若要在群集上重新激活所有 Azure Arc 功能，请删除并重新创建已启用 Azure Arc 的 Kubernetes 资源和代理。 在 90 天内，每隔 30 天至少连接群集一次。 |
| 已断开连接 | 已启用 Azure Arc 的 Kubernetes 目前不支持离线环境中无法访问 Azure 的 Kubernetes 群集。 如果你对此功能感兴趣，请在 [Azure Arc 的 UserVoice 论坛](https://feedback.azure.com/forums/925690-azure-arc)中提交看法，或者对相关看法投赞同票。

## <a name="next-steps"></a>后续步骤

* 参考我们的快速入门，[将 Kubernetes 群集连接到 Azure Arc](./quickstart-connect-cluster.md)。
* 详细了解如何在群集与 Git 存储库之间创建连接，作为[已启用 Azure Arc 的 Kubernetes 中的配置资源](./conceptual-configurations.md)。