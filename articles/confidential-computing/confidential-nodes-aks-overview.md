---
title: Azure Kubernetes 服务 (AKS) 上的机密计算节点
description: AKS 上的机密计算节点
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 9205513c4eb7e377fee0c5d18577d76a82476cf2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553385"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Azure Kubernetes 服务上的机密计算节点

使用敏感数据时，可使用 [Azure 机密计算](overview.md)保护敏感数据。 底层机密计算基础结构通过硬件支持的受信任执行容器环境来保护此数据不受其他应用程序、管理员和云提供商影响。 通过添加机密计算节点，可以将容器应用程序定位为在隔离的、受硬件保护的和可证明的环境中运行。

## <a name="overview"></a>概述

Azure Kubernetes 服务 (AKS) 支持添加由 Intel SGX 提供支持的 [DCsv2 机密计算节点](confidential-computing-enclaves.md)。 这些节点使你可以在基于硬件的受信任执行环境 (TEE) 中运行敏感工作负载。 TEE 允许容器中的用户级代码分配内存的专用区域，以便直接通过 CPU 执行代码。 这些直接通过 CPU 执行的专用内存区域称为 enclave。 Enclave 有助于保护数据机密性、数据完整性和代码完整性，使其不受在同一节点上运行的其他进程的影响。 Intel SGX 执行模型还删除了来宾 OS、主机 OS 和虚拟机监控程序中间层，从而减攻击面。 节点中基于每个容器的硬件独立执行模型允许应用程序直接在 CPU 中执行，同时使每个容器的特殊内存块保持加密。 带有机密容器的机密计算节点是对零信任安全性计划和深层防御容器策略的极佳补充。

![sgx 节点概述](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS 机密节点功能

- 基于硬件的进程级容器可通过 Intel SGX 受信任执行环境 (TEE) 实现隔离 
- 异源节点池群集（混合机密和非机密节点池）
- 基于加密页面缓存 (EPC) 内存的 pod 计划（需要外接程序）
- 预安装 Intel SGX DCAP 驱动程序
- 基于 CPU 消耗的水平 pod 自动缩放和群集自动缩放
- 通过 Ubuntu 18.04 Gen 2 VM 工作器节点的 Linux 容器支持

## <a name="confidential-computing-add-on-for-aks"></a>适用于 AKS 的机密计算外接程序
在群集上运行机密计算节点池时，外接程序功能将在 AKS 上启用额外功能。 此外接程序可启用以下功能。

#### <a name="azure-device-plugin-for-intel-sgx"></a>适用于 Intel SGX <a id="sgx-plugin"></a> 的 Azure 设备插件

该设备插件为加密页面缓存 (EPC) 内存实现 Kubernetes 设备插件接口，并从节点公开设备驱动程序。 实际上，此插件会使 EPC 内存成为 Kubernetes 中的另一种资源类型。 用户可以指定此资源的限制，就像指定其他资源一样。 除了计划函数，设备插件还有助于为机密工作负载容器分配 Intel SGX 设备驱动程序权限。 借助此插件，开发人员可以避免在部署文件中装载 Intel SGX 驱动程序卷。 [此处](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)有基于 EPC 内存的部署 (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) 的示例实现


## <a name="programming-models"></a>编程模型

### <a name="confidential-containers"></a>机密容器

[机密容器](confidential-containers.md)可帮助以机密方式运行大多数常见编程语言运行时（Python、Node、Java 等）的现有未修改容器应用程序。 此打包模型无需修改或重新编译源代码。 这是实现机密性最快的方法，可以通过将标准 Docker 容器与开源项目或 Azure 合作伙伴解决方案打包来实现。 在此打包和执行模型中，容器应用程序的所有部分都将加载到受信任的边界 (enclave) 中。 此模型非常适合市场上可用的现成容器应用程序或当前在常规用途节点上运行的自定义应用程序。

### <a name="enclave-aware-containers"></a>Enclave 感知容器
AKS 上的机密计算节点还支持编程为在 enclave 中运行以利用 CPU 中可用的特殊指令集的容器。 此编程模型可以对执行流进行更严格的控制，并且需要使用特殊 SDK 和框架。 此编程模型以最低的可信计算基础 (TCB) 为应用程序流提供最大程度的控制。 Enclave 感知容器开发涉及到容器应用程序的不受信任和受信任的部分，从而使你能够管理执行 enclave 的常规内存和加密页面缓存 (EPC) 内存。 [详细了解](enclave-aware-containers.md) enclave 感知容器。

## <a name="next-steps"></a>后续步骤

[使用机密计算节点部署 AKS 群集](./confidential-nodes-aks-get-started.md)

[机密容器快速入门示例](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 列表](../virtual-machines/dcv2-series.md)

[利用机密容器进行深层防御的网络研讨会](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
