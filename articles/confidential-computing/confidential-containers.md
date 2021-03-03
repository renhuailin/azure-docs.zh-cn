---
title: 'Azure Kubernetes Service 上的机密容器 (AKS) '
description: 了解机密容器上的未修改容器支持。
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: a5db93f096c73679c1b6b6ae464897db843f2e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706259"
---
# <a name="confidential-containers"></a>机密容器

## <a name="overview"></a>概述

允许开发人员将 **现有 docker 应用程序 (新的或现有的)** ，并通过机密计算节点支持安全地在 Azure Kubernetes 服务 (AKS) 上运行该应用程序。

机密容器有助于保护：

- 数据完整性 (data integrity) 
- 数据保密性
- 代码完整性
- 通过加密来保护容器代码
- 基于硬件的保障
- 允许运行现有应用
- 创建信任的硬件根
- 从信任边界中删除主机管理员、Kubernetes 管理员、虚拟机监控程序

基于硬件的受信任执行环境 (t) 是一项重要的组件，用于通过来自受信任计算基础的硬件和软件度量 (TCB) 组件来提供强大的保障。 验证这些度量值有助于验证预期的计算，并验证容器应用的任何篡改。

机密容器支持通过 **Python、Java、NODE JS 等开发的自定义应用程序或打包的容器应用程序（如 NGINX、Redis 缓存、MemCache** 等）在具有机密计算节点的 AKS 上以未修改的方式运行。

机密容器是容器机密性的最快途径，只需要重新打包现有的 docker 容器应用程序，而不需要更改应用程序代码。 机密容器是打包为在容器中运行的 docker 容器应用程序。 某些机密容器启用码还提供容器加密，可帮助在存储和传输过程中保护容器代码，并在主机上装入。 容器加密可让你进一步，并保护在容器中打包的代码/模型，并将其解密密钥附加到该容器。

下面是用于部署机密容器以部署 ![ 机密容器如何处理的过程。](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>机密容器启用码
若要运行未修改的现有 docker 容器，需要使用 SGX 软件，以便应用程序调用可以使用特殊的 CPU 指令集来降低附加 surface 区域，且不会依赖来宾操作系统。 一旦用 SGX 运行时软件进行包装，容器就会在受保护的 enclaves 中自动启动，从而从信任边界中删除来宾操作系统、主机 OS 或虚拟机监控程序。 该节点 (虚拟机) 中的这一独立执行与硬件支持的内存中数据加密，可减少整体 surface 攻击区域，降低操作系统或虚拟机监控程序层的漏洞。

机密容器在 AKS 上完全受支持，并通过 Azure 合作伙伴和 Open-Source 软件 (OSS) 项目来启用。 开发人员可以根据功能选择软件提供程序、与 Azure 服务和工具支持的集成。

## <a name="partner-enablers"></a>合作伙伴启用码
> [!NOTE]
> 以下解决方案通过 Azure 合作伙伴提供，并可能会产生许可费用。 请独立验证合作伙伴软件条款。 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) 提供了 SGX 平台软件，使你能够在 AKS 上运行未修改的容器。 了解更多有关功能的信息，并查看 [此处](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)的示例应用程序。

[在此处](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)开始使用示例 Redis 缓存和 Python 自定义应用程序

![Anjuna 进程](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) 为开发人员提供了一种基于门户和 CLI 的体验，使其成为容器化的应用程序，并将其转换为受 SGX 支持的机密容器，而无需修改或重新编译应用程序。 Fortanix 提供了运行和管理最广泛的应用程序集的灵活性，包括现有应用程序、新的 enclave 和本机应用程序以及预打包的应用程序。 用户可以使用 [机密计算管理器](https://em.fortanix.com/) UI 或 [REST Api](https://www.fortanix.com/api/em/) 从 Azure Kubernetes 服务的 [快速入门](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) 指南开始创建机密容器。

![Fortanix 部署过程](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain) 

[SCONE](https://scontain.com/index.html?lang=en) 支持可生成证书、密钥和机密的安全策略，并确保它们只对应用程序的证明服务可见。 通过这种方式，应用程序的服务可以通过 TLS 自动证明彼此，无需修改应用程序和 TLS。 下面是一个简单的 Flask 应用程序的帮助说明： https://sconedocs.github.io/flask_demo/  

SCONE 可以将现有的大多数二进制文件转换为在 enclaves 内运行的应用程序，而无需更改应用程序或重新编译该应用程序。 SCONE 还通过对数据文件和 Python 代码文件进行 **加密** 来保护 Python 等解释性语言。 通过 SCONE 安全策略的帮助，可以保护加密文件不受未经授权的访问、修改和回滚。 [此处](https://sconedocs.github.io/sconify_image/)介绍了如何 "sconify" 现有的 Python 应用程序

![Scontain Flow](./media/confidential-containers/scone-workflow.png)

具有 AKS 的机密计算节点上的 Scone 部署完全受支持，并与其他 Azure 服务集成。 在此处开始使用示例应用程序 https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>OSS 启用码 
> [!NOTE]
> 以下解决方案通过 Open-Source 项目提供，不直接与 Azure 机密计算相关 (ACC) 或 Microsoft。  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) 是一种轻型来宾操作系统，旨在运行具有最低主机要求的单个 Linux 应用程序。 Graphene 可以在隔离的环境中运行应用程序，优点相当于运行完整的 OS，并且具有良好的工具支持，可将现有的 docker 容器应用程序转换为 Graphene 屏蔽容器 (GSC) 。

[AKS 上](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)的示例应用程序和部署入门

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) 是适用于 Intel SGX 的内存安全、多进程库 OS (LibOS) 。 它使旧版应用程序能够在 SGX 上运行，几乎不会对源代码进行修改。 Occlum 以透明方式保护用户工作负荷的机密性，同时允许轻松提升现有 docker 应用程序的过程。

Occlum 支持 AKS 部署。 在[此处](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)遵循各种示例应用的部署说明


## <a name="confidential-containers-demo"></a>机密容器演示
查看机密容器的机密保健演示。 [此处](/azure/architecture/example-scenario/confidential/healthcare-inference)提供了示例。 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>获取触控

您的实现有疑问，还是想成为启用码？ 向产品团队发送电子邮件 **acconaks@microsoft.com**

## <a name="reference-links"></a>引用链接

[Microsoft Azure 证明](../attestation/overview.md)

[DCsv2 虚拟机](virtual-machine-solutions.md)

[Azure Kubernetes 服务 (AKS)](../aks/intro-kubernetes.md)