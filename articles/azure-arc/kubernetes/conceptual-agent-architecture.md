---
title: 已启用 Azure Arc Kubernetes 代理体系结构
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 本文提供支持 Azure Arc 的 Kubernetes 代理的体系结构概述
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561357"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>已启用 Azure Arc Kubernetes 代理体系结构

[Kubernetes](https://kubernetes.io/) 可用于在混合和多云环境中以一致的方式部署容器化工作负荷。 启用 Azure Arc 后，可以将 Kubernetes 用作集中控制平面，以便在这些异构环境中持续管理策略、治理和安全性。 本文提供以下内容：

* 将群集连接到 Azure 的体系结构概述。
* 后跟代理的连接模式。
* 对群集环境和 Azure 之间交换的数据的说明。

## <a name="deploy-agents-to-your-cluster"></a>将代理部署到群集

大多数本地数据中心强制实施严格的网络规则，阻止在网络边界使用防火墙上的入站通信。 启用 Azure Arc 后，只需为出站通信启用选择性出口终结点，而无需防火墙上的任何入站端口，即可使用 Azure Arc Kubernetes。 启用 Azure Arc 的 Kubernetes 代理将启动出站连接。

![体系结构概述](./media/architectural-overview.png)

使用以下步骤将群集连接到 Azure Arc：

1. 根据所选的基础结构 (VMware vSphere、Amazon Web Services、Google Cloud Platform 等 ) 创建 Kubernetes 群集。 

    > [!NOTE]
    > 客户需要创建和管理 Kubernetes 群集自身的生命周期，因为 Azure Arc enabled Kubernetes 目前仅支持将现有的 Kubernetes 群集附加到 Azure Arc。  

1. 使用 Azure CLI 启动群集的 Azure Arc 注册。
    * Azure CLI 使用 Helm 在群集上部署代理 Helm 图表。
    * 群集节点启动到 [Microsoft 容器注册表](https://github.com/microsoft/containerregistry) 的出站通信，并请求在命名空间中创建以下代理所需的映像 `azure-arc` ：

        | Agent | 说明 |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | 启用 Azure Arc 的 Kubernetes 目前仅支持 [系统分配的标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 clusteridentityoperator 使得获取托管服务标识 (MSI) 证书所需的第一个出站通信，以便其他代理与 Azure 通信。 |
        | `deployment.apps/config-agent` | 监视连接的群集，以了解在群集上应用的源代码管理配置资源并更新符合性状态 |
        | `deployment.apps/controller-manager` | 协调 Azure Arc 组件之间的交互的运算符运算符 |    
        | `deployment.apps/metrics-agent` | 收集其他 Arc 代理的指标，以确保这些代理表现最佳 |
        | `deployment.apps/cluster-metadata-operator` | 收集分类元数据-群集版本、节点计数和 Azure Arc 代理版本 |
        | `deployment.apps/resource-sync-agent` | 将上面提到的群集元数据同步到 Azure |
        | `deployment.apps/flux-logs-agent` | 从部署为源代码管理配置一部分的 flux 操作员收集日志 |
    
1. 启用所有 Azure Arc Kubernetes 代理后 `Running` ，请验证群集是否已连接到 Azure arc。应会看到：
    * Azure Arc 启用了 [azure 资源管理器](../../azure-resource-manager/management/overview.md)中的 Kubernetes 资源。 此资源在 Azure 中跟踪为客户托管的 Kubernetes 群集的投影，而不是实际 Kubernetes 群集本身。
    * 群集元数据（如 Kubernetes 版本、代理版本和节点数）将在 Azure Arc enabled Kubernetes 资源上显示为元数据。

## <a name="data-exchange-between-cluster-environment-and-azure"></a>群集环境与 Azure 之间的数据交换

| 数据类型 | 方案 | 通信模式 |
| --------- | -------- | ------------------ |
| Kubernetes 群集版本 | 分类元数据 | 代理推送到 Azure |
| 群集中的节点数 | 分类元数据 | 代理推送到 Azure |
| 代理版本 | 分类元数据 | 代理推送到 Azure |
| Kubernetes 分发类型 | 分类元数据 | Azure CLI 推送到 Azure |
| 基础结构类型 (AWS/GCP/vSphere/... )  | 分类元数据 | Azure CLI 推送到 Azure |
| 群集中节点的 vCPU 计数 | 计费 | Azure CLI 推送到 Azure |
| 代理检测信号 | 资源运行状况 | 代理推送到 Azure |
| 代理)  (内存/CPU 的资源消耗 | 诊断和可支持性 | 代理推送到 Azure |
| 所有代理容器的日志 | 诊断和可支持性 | 代理推送到 Azure |
| 代理升级可用性 | 代理升级 | 从 Azure 提取代理 |
| 所需的配置-Git 存储库 URL，flux 运算符参数，私钥，已知主机内容，HTTPS 用户名，令牌/密码 | Configuration | 从 Azure 提取代理 |
| Flux 操作员安装状态 | Configuration | 代理推送到 Azure |
| 需要在群集内执行网关守卫强制的 Azure 策略分配 | Azure Policy | 从 Azure 提取代理 |
| 群集中策略操作的审核和符合性状态 | Azure Policy | 代理推送到 Azure |
| 客户工作负荷的指标和日志 | Azure Monitor | 代理推送到客户租户和订阅中 Log Analytics 工作区资源 |

## <a name="connectivity-status"></a>连接性状态

| 状态 | 说明 |
| ------ | ----------- |
| Connecting | Azure Arc 已启用 Kubernetes 在 Azure 资源管理器中创建的资源，但服务尚未收到代理检测信号。 |
| 连续 | 启用 Azure Arc 后，Kubernetes 服务将在过去15分钟内收到代理检测信号。 |
| Offline | 已启用 Azure Arc Kubernetes 资源以前已连接，但该服务未接收到任何代理检测信号15分钟。 |
| 已过期 | 托管服务标识 (MSI) 证书在发出后的过期时段为90天。 此证书过期后，会考虑资源， `Expired` 所有功能（如配置、监视和策略）都将停止在此群集上运行。 若要详细了解如何处理启用了过期的 Azure Arc Kubernetes 资源，可在[此处](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)找到 |

## <a name="understand-connectivity-modes"></a>了解连接模式

| 连接模式 | 说明 |
| ----------------- | ----------- |
| 完全连接 | 代理始终能够访问 Azure。 在这种情况下，体验非常理想，因为在传播 GitOps) 的配置时，会有一些延迟， (在 Azure 策略和网关守卫) 和收集 (Azure Monitor 中的工作负荷的指标和日志 ( |
| 半连接 | 由拉取的 MSI 证书在 `clusteridentityoperator` 证书过期之前的最大有效期为90天。 证书过期后，启用了 Azure Arc 的 Kubernetes 资源将停止工作。 删除并重新创建启用了 Azure Arc 的 Kubernetes 资源和代理，以获得要在群集上运行的所有弧线功能。 在90天内，建议用户至少每30天连接一次群集。 |
| 已断开连接 | 当前不支持 azure Arc 启用 Kubernetes 的已断开连接的环境中的 Kubernetes 群集。 如果你对此功能感兴趣，请在 [Azure Arc 的 UserVoice 论坛](https://feedback.azure.com/forums/925690-azure-arc)上提交或向上投票。

## <a name="next-steps"></a>后续步骤

* [将群集连接到 Azure Arc](./connect-cluster.md)
* [配置概念概述](./conceptual-configurations.md)