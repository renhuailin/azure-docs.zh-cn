---
title: 与 Azure Kubernetes 服务 (AKS) 上的机密节点支持相关的常见问题解答
description: 查找一些与 Azure Kubernetes 服务 (AKS) 和 Azure 机密计算 (ACC) 节点支持相关的常见问题的解答。
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 94b585078ce5e78a658fd8f110f09963200010b6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933553"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 上的机密计算节点的常见问题解答

本文解决了有关 Azure Kubernetes 服务 (AKS) 上基于 Intel SGX 的机密计算节点的常见问题。 如果你有任何其他问题，请发送电子邮件至 acconaks@microsoft.com。

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>AKS 上的机密计算节点是否已正式发布？ ###
是

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>什么是证明，如何对 enclave 中运行的应用进行证明？ ###
证明是指演示和验证某个软件是否已在特定硬件平台上正确实例化的过程。 它还可确保其证据是可验证的，从而确保软件在安全平台中运行且未被篡改。 [详细了解](attestation.md)如何对 enclave 应用进行证明。

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>能否使用 Azure 机密计算 AKS 群集启用加速网络？ ###
不知道。 构成 AKS 上机密计算节点的 DCSv2 虚拟机不支持加速网络。 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>是否可以引入现有容器化应用程序，并通过 Azure 机密计算在 AKS 上运行该应用程序？ ###
是，请查看[机密容器页](confidential-containers.md)，了解有关平台启用程序的更多详细信息。

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>机密节点的 AKS 映像上是哪个版本的 Intel SGX 驱动程序版本？ ### 
目前，Azure 机密计算 DCSv2 VM 在安装时带有 Intel SGX DCAP 1.33。 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>是否可以将安装后脚本/自定义驱动程序注入到 AKS 预配的节点？ ###
不知道。 [基于 AKS 引擎的机密计算节点](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md)支持可实现自定义安装并对 Kubernetes 控制平面拥有完全控制权的机密计算节点。
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>最开始使用 enclave 应用程序时我是否应该使用 Docker 基本映像？ ###
各种启用程序（ISV 和 OSS 项目）提供了各种启用机密容器的方法。 请查看[机密容器页](confidential-containers.md)，了解更多详细信息以及有关实现的单独参考内容。

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>能否使用其他标准 AKS SKU 来运行 ACC 节点（生成异构节点池群集）？ ###

能，可以在同一 AKS 群集中运行不同的节点池，包括 ACC 节点。 要使 enclave 应用程序面向特定节点池，可考虑添加节点选择器或应用 EPC 限制。 请参阅[此处](confidential-nodes-aks-get-started.md)，了解有关机密节点的快速入门的更多详细信息。

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>能否通过 ACC 运行 Windows 节点和 Windows 容器？ ###
目前没有。 如果你有 Windows 节点或容器需求，请通过 acconaks@microsoft.com 联系产品团队。 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>如果容器大小超过可用 EPC 内存，该怎么办？ ###
EPC 内存应用于为了在 enclave 中执行而经过了编程的应用程序部分。 容器的总大小并不是指示最大可用 EPC 内存的指标。 事实上，包含 SGX 的 DCSv2 计算机允许应用程序的不受信任部分可利用的最大 VM 内存为 32 GB。 但是，如果容器使用的内存超出可用的 EPC 内存，则在 enclave 中运行的程序部分的性能可能会受到影响。

若要更好地管理工作器节点中的 EPC 内存，可考虑通过 Kubernetes 实现的基于 EPC 内存的限制管理。 请参考以下示例。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>如果 enclave 使用的内存超过最大可用 EPC 内存，会发生什么情况？ ###

同一 VM 或工作器节点中的 enclave 应用程序共用全部可用的 EPC 内存。 如果应用程序使用的 EPC 内存超出可用内存，则应用程序性能可能会受到影响。 出于此原因，建议在部署 yaml 文件中设置每个应用程序的容忍度，以更好地管理每个工作器节点的可用 EPC 内存，如以上示例中所示。 或者，始终可以选择提升工作器节点池 VM 大小或添加更多节点。 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>为什么无法执行 forks () 和 exec 以在 enclave 应用程序中运行多个进程？ ###

目前，Azure 机密计算 DCsv2 SKU VM 支持为 enclave 中执行的程序使用单个地址空间。 单进程是围绕高安全性设计的当前限制。 但是，机密容器启用程序可能具有替代实现来克服此限制。
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>是否自动安装了任何其他守护程序集以公开 SGX 驱动程序？ ###

是的。 守护程序集的名称为 sgx-device-plugin。 在[此处](confidential-nodes-aks-overview.md)了解有关其各自用途的详细信息。  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>应该为机密计算节点选择什么 VM SKU？ ###

DCSv2 SKU。 [支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)中提供了 [DCSv2 SKU](../virtual-machines/dcv2-series.md)。

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>是否仍可以在机密计算节点上计划并运行非 enclave 容器？ ###

是的。 VM 还具有可运行标准容器工作负载的常规内存。 在确定部署模型之前，请考虑应用程序的安全性和威胁模型。
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>是否可以通过 Azure 门户使用 DCSv2 节点池来预配 AKS？ ###

是的。 还可以使用 Azure CLI，如[此处](confidential-nodes-aks-get-started.md)所述。

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>支持哪个 Ubuntu 版本和 VM 代系？ ###
第 2 代上的 18.04。 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>能否更改 AKS 上的当前 Intel SGX DCAP 驱动程序版本？ ###

不知道。 若要执行任何自定义安装，建议选择 [AKS 引擎机密计算工作器节点](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md)部署。 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>支持和建议哪个版本的 Kubernetes？ ###

支持和建议 Kubernetes 版本 1.16 及更高版本。 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>产品的已知当前限制是什么？ ###

- 仅支持 Ubuntu 18.04 Gen 2 VM 节点 
- 不支持 Windows 节点或 Windows 容器
- 不支持基于 EPC 内存的水平 Pod 自动缩放。 支持基于 CPU 和常规内存的缩放。
- 目前不支持机密应用的 AKS 上的开发空间

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>是否在 enclave 中仅加载已签名和受信任的映像用于机密计算？ ###
这种情况在 enclave 初始化过程中不是原生存在的，但可以通过证明过程验证签名。 请参阅[此处](../attestation/basic-concepts.md#benefits-of-policy-signing)。 

### <a name="next-steps"></a>后续步骤
查看[机密容器页](confidential-containers.md)，了解有关机密容器的更多详细信息。
