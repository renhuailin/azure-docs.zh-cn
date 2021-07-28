---
title: Azure 安全中心和 Azure Defender 的容器安全性
description: 了解 Azure 安全中心的容器安全功能
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 17a590be2038acc56656d91cf11230b0782e1c81
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112239017"
---
# <a name="container-security-in-security-center"></a>安全中心的容器安全性

Azure 安全中心是用于保护容器安全的 Azure 原生解决方案。

安全中心可以保护以下类型的容器资源：

| 资源类型 | 安全中心提供的保护 |
|:--------------------:|-----------|
| ![Kubernetes 服务。](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Kubernetes 群集** | 持续评估群集，以直观显示错误配置，并提供帮助缓解发现的威胁的指导信息。 详细了解如何[通过安全建议强化环境](#environment-hardening)。<br><br>面向群集和 Linux 节点的威胁防护。 针对可疑活动的警报由 [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) 提供。 此 Azure Defender 计划可保护你的 Kubernetes 群集，不管它们是托管在 Azure Kubernetes 服务(AKS) 中、本地还是在其他云提供商的平台上 。 <br>详细了解[面向 Kubernetes 节点和群集的运行时保护](#run-time-protection-for-kubernetes-nodes-and-clusters)。|
| ![容器主机。](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**容器主机**<br>（运行 Docker 的 VM） | 持续评估 Docker 环境，以直观显示错误配置并提供相关指导信息，帮助你缓解可选的[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 发现的威胁。<br>详细了解如何[通过安全建议强化环境](#environment-hardening)。|
| ![容器注册表。](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure 容器注册表 (ACR) 的注册表** | 通过可选的[适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)，提供漏洞评估和管理工具，这些工具可用于基于 Azure 资源管理器的 ACR 注册表中的映像。<br>详细了解如何[扫描容器映像漏洞](#vulnerability-management---scanning-container-images)。 |
|||

本文介绍如何将安全中心与适用于容器注册表、服务器和 Kubernetes 的 Azure Defender 计划（可选）结合使用，来改善、监视和维护容器及其应用的安全性。

你将了解 Azure 安全中心如何在容器安全性的以下核心方面提供帮助：

- [漏洞管理 - 扫描容器映像](#vulnerability-management---scanning-container-images)
- [环境强化](#environment-hardening)
- [面向 Kubernetes 节点和群集的运行时保护](#run-time-protection-for-kubernetes-nodes-and-clusters)

以下屏幕截图显示了“资产清单”页以及受安全中心保护的各种类型的容器资源。

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="安全中心“资产清单”页中与容器相关的资源" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>漏洞管理 - 扫描容器映像

若要监视基于 Azure 资源管理器的 Azure 容器注册表中的映像，请启用[适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)。 安全中心扫描在过去 30 天内拉取的、推送到注册表中或导入的任何映像。 集成扫描程序由业界领先的漏洞扫描供应商 Qualys 提供。

当 Qualys 或安全中心发现问题时，你将在 [Azure Defender 仪表板](azure-defender-dashboard.md)中收到通知。 安全中心会针对每个漏洞提供可行的建议、严重性分类，以及有关如何修正问题的指南。 若要详细了解安全中心针对容器提供的建议，请参阅[建议的参考列表](recommendations-reference.md#recs-compute)。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

## <a name="environment-hardening"></a>环境强化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持续监视 Docker 配置

Azure 安全中心会识别在 IaaS Linux VM 上或其他运行 Docker 容器的 Linux 计算机上承载的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 [Internet 安全中心 (CIS) 的 Docker 基准](https://www.cisecurity.org/benchmark/docker/)进行比较。

安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 在发现错误配置时，安全中心会生成安全建议。 使用安全中心的建议页面来查看建议和修正问题。 不会对 AKS 托管的实例或 Databricks 托管的 VM 运行 CIS 基准检查。

若要详细了解针对此功能可能出现的相关安全中心建议，请参阅建议参考表的[计算部分](recommendations-reference.md#recs-compute)。

浏览 VM 的安全问题时，安全中心会提供计算机上有关容器的其他信息。 此类信息包括 Docker 版本以及主机上运行的映像数。 

若要监视 IaaS Linux VM 上承载的非托管容器，请启用可选的[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>持续监视 Kubernetes 群集
安全中心可以与 Azure Kubernetes 服务 (AKS) 协同工作，后者是 Microsoft 的托管容器业务流程服务，用于开发、部署和管理容器化应用程序。

AKS 提供安全控制，并且可用于了解群集的安全状况。 安全中心使用这些功能来持续监视 AKS 群集的配置，并生成符合行业标准的安全建议。

下面是有关 Azure 安全中心、Azure Kubernetes 服务和 Azure Policy 之间的交互的高级关系图：

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="有关 Azure 安全中心、Azure Kubernetes 服务和 Azure Policy 之间的交互的高级关系图" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

你可以看到安全中心接收和分析的项包括：

- 来自 API 服务器的审核日志
- Log Analytics 代理中的原始安全事件

    > [!NOTE]
    > 目前，我们不支持在虚拟机规模集上运行的 Azure Kubernetes 服务群集上安装 Log Analytics 代理。

- 来自 AKS 群集的群集配置信息
- Azure Policy 中的工作负载配置（通过适用于 Kubernetes 的 Azure Policy 加载项）

若要详细了解针对此功能可能出现的相关安全中心建议，请参阅建议参考表的[计算部分](recommendations-reference.md#recs-compute)。


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>使用 Kubernetes 准入控制实现工作负载保护最佳做法

安装适用于 Kubernetes 的 Azure Policy 加载项，获取一系列有助于保护 Kubernetes 容器工作负载的建议。 还可以根据[启用 Log Analytics 代理和扩展的自动预配](security-center-enable-data-collection.md#auto-provision-mma)中的说明，自动部署此加载项。 将加载项的自动预配设置为“启用”时，默认情况下会在所有现有和未来的群集（满足加载项安装要求）中启用该扩展。

如[此用于 Kubernetes 的 Azure Policy 页](../governance/policy/concepts/policy-for-kubernetes.md)中所述，加载项扩展了 [开放策略代理](https://www.openpolicyagent.org/)的开源 [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) 准入控制器 webhook。 Kubernetes 准入控制器是强制实施群集使用方式的插件。 此加载项注册为 Kubernetes 准入控制的 web 挂钩，并使你能够以集中一致的方式在群集上应用大规模强制性操作和安全措施。 

通过 AKS 群集上的加载项，将按照预先定义的一组最佳做法监视对 Kubernetes API 服务器的每个请求，然后再将其保存到群集。 然后，可以配置为强制实施最佳做法，并规定将其用于未来的工作负载。 

例如，可以规定不应创建特权容器，并且阻止以后的任何请求。

参阅[保护 Kubernetes 工作负载](kubernetes-workload-protections.md)，了解详细信息。


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>面向 Kubernetes 节点和群集的运行时保护

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>后续步骤

通过此概述性介绍，你了解了 Azure 安全中心容器安全性的核心元素。 如需查看相关材料，请参阅：

- [适用于 Kubernetes 的 Azure Defender 简介](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender 简介](defender-for-container-registries-introduction.md)