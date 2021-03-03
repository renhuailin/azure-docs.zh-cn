---
title: 用 Container insights 配置 GPU 监视 |Microsoft Docs
description: 本文介绍如何配置通过支持 NVIDIA 和 AMD GPU 的节点和容器 insights 来监视 Kubernetes 群集。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731861"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>用 Container insights 配置 GPU 监视

从代理版本 *ciprod03022019* 开始，Container insights 集成代理现在支持监视 gpu (图形处理单元) 使用 Gpu 感知 Kubernetes 群集节点，以及监视请求和使用 GPU 资源的 pod/容器。

## <a name="supported-gpu-vendors"></a>支持的 GPU 供应商

Container insights 支持监视以下 GPU 供应商提供的 GPU 群集：

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

容器见解会自动开始监视节点上的 GPU 使用情况，并通过在60sec 间隔收集以下度量值并将其存储在 **InsightMetrics** 表中，来请求 pod 和工作负荷。

>[!NOTE]
>预配具有 GPU 节点的群集后，请确保根据 AKS 的要求安装 [GPU 驱动程序](../../aks/gpu-cluster.md)以运行 GPU 工作负载。 Container insights 通过节点中运行的 GPU 驱动程序箱收集 GPU 指标。 

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor|在刚过去的采样周期（60 秒）中，GPU 处于繁忙/积极处理容器的状态的时间百分比。 占空比是 1 到 100 之间的数字。 |
|containerGpuLimits |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName |每个容器可以将限值指定为一个或多个 GPU。 不能请求或限制为 GPU 的一部分。 |
|containerGpuRequests |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName |每个容器可以请求一个或多个 GPU。 不能请求或限制为 GPU 的一部分。|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor |可用于特定容器的 GPU 内存量（以字节为单位）。 |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor |特定容器使用的 GPU 内存量（以字节为单位）。 |
|nodeGpuAllocatable |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |节点中可供 Kubernetes 使用的 GPU 数。 |
|nodeGpuCapacity |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |节点中的 GPU 总数。 |

## <a name="gpu-performance-charts"></a>GPU 性能图表 

容器见解包括表中前面列出的指标的预配置图表，作为每个群集的 GPU 工作簿。 有关可用于容器见解的工作簿的说明，请参阅 [容器见解中的工作簿](../insights/container-insights-reports.md) 。

## <a name="next-steps"></a>后续步骤

- 请参阅[在 Azure Kubernetes 服务 (AKS) 上将 GPU 用于计算密集型工作负荷](../../aks/gpu-cluster.md)，以了解如何部署包含支持 GPU 的节点的 AKS 群集。

- 详细了解 [Microsoft Azure 中的 GPU 优化 VM sku](../../virtual-machines/sizes-gpu.md)。

- 请查看 [Kubernetes 中的 GPU 支持](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)，了解有关在群集中的一个或多个节点上管理 GPU 的 Kubernetes 试验性支持。
