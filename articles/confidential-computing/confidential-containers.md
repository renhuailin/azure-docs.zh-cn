---
title: Azure Kubernetes 服务 (AKS) 上的机密容器
description: 了解机密容器的“未修改的容器”支持。
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 35bf2fc96f93d1eed2b15c4ea87aa1f2a3aaa0a5
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113002881"
---
# <a name="confidential-containers"></a>机密容器

## <a name="overview"></a>概述

允许开发人员引入一个 **docker 应用程序（新的或现有的）** ，并通过机密计算节点支持在 Azure Kubernetes 服务 (AKS) 上安全地运行它。

机密容器用于：

- 数据完整性 (data integrity) 
- 保护数据机密性
- 保护代码完整性
- 通过加密保护容器代码
- 提供基于硬件的保障
- 允许运行现有的应用
- 创建硬件信任根
- 从信任边界中删除主机管理员、Kubernetes 管理员、虚拟机监控程序

基于硬件的受信任执行环境 (TEE) 是一个重要的组件，用于通过来自受信任计算基础 (TCB) 组件的硬件和软件度量来提供强大的保障。 验证这些度量有助于验证预期的计算，并可验证对容器应用所做的任何篡改。

机密容器支持在带有机密计算节点的 AKS 上运行通过 Python、Java、Node JS 等开发的自定义应用程序或打包的容器应用程序（例如 NGINX、Redis 缓存、MemCache 等），而无需对这些应用程序进行任何修改。

机密容器是实现容器机密性的最快途径，只需要重新打包现有的 docker 容器应用程序，不需要更改应用程序代码。 机密容器是已打包以便在 TEE 中运行的 docker 容器应用程序。 某些机密容器启用程序还提供容器加密，用于在存储和传输过程中以及在装载到主机时保护容器代码。 容器加密可让你更进一步，保护在容器中打包的代码/模型，并将其解密密钥附加到 TEE。

下面是机密容器从开发到部署的过程 ![机密容器的具体过程。](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>机密容器启用程序
若要运行现有 docker 容器而无需对其进行修改，需要使用 SGX 软件，以便应用程序调用可以使用提供的特殊 CPU 指令集来减少附加外围应用，不依赖来宾 OS。 一旦使用 SGX 运行时软件进行包装，容器就会在受保护的 enclave 中自动启动，从而从信任边界中删除来宾 OS、主机 OS 或虚拟机监控程序。 某个节点（虚拟机）中的这种独立执行操作与硬件支持的内存中数据加密配合使用，可在整体上减少受攻击外围应用，并会减少操作系统或虚拟机监控程序层的漏洞。

机密容器在 AKS 上完全受支持，通过 Azure 合作伙伴和开源软件 (OSS) 项目来启用。 开发人员可以根据功能、与 Azure 服务的集成以及工具支持来选择软件提供商。

## <a name="partner-enablers"></a>合作伙伴启用程序
> [!NOTE]
> 以下解决方案通过 Azure 合作伙伴提供，可能会产生许可费用。 请独立验证合作伙伴软件条款。 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) 提供 SGX 平台软件，使你能够在 AKS 上运行未修改的容器。 在[此处](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)详细了解功能并查看示例应用程序。

在[此处](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)开始使用示例 Redis 缓存和 Python 自定义应用程序

![Anjuna 流程](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) 允许开发人员选择门户和基于 CLI 的体验来引入容器化应用程序，并将其转换为支持 SGX 的机密容器，而无需修改或重新编译应用程序。 Fortanix 提供运行和管理最广泛应用程序集（包括现有的应用程序、新的 enclave 原生应用程序以及预打包的应用程序）所需的灵活性。 用户可以从[机密计算管理器](https://em.fortanix.com/) UI 或 [REST API](https://www.fortanix.com/api/em/) 着手，按照 Azure Kubernetes 服务的[快速入门](https://fortanix.com/blog/2020/10/fortanix-confidential-containers-on-microsoft-azure-kubernetes-service-aks/)指南来创建机密容器。

![Fortanix 部署过程](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[SCONE](https://scontain.com/index.html?lang=en) 支持可以生成证书、密钥和机密的安全策略，并确保它们只对经过证明的应用程序服务可见。 通过这种方式，应用程序的服务可以通过 TLS 自动地互相证明，无需修改应用程序和 TLS。 可以通过下面这个简单的 Flask 应用程序来说明这一点： https://sconedocs.github.io/flask_demo/  

SCONE 可以将现有的大多数二进制文件转换为在 enclave 内运行的应用程序，无需更改应用程序或重新编译该应用程序。 SCONE 还通过对数据文件和 Python 代码文件进行 **加密** 来保护 Python 之类的解释语言。 借助 SCONE 安全策略，用户可以防止对加密文件进行未经授权的访问、修改和回退。 [此文](https://sconedocs.github.io/sconify_image/)介绍了如何将现有的 Python 应用程序“scone 化”

![Scontain 流](./media/confidential-containers/scone-workflow.png)

AKS 的机密计算节点上的 Scone 部署在其他 Azure 服务中完全受支持，并已与这些服务集成。 开始使用 https://sconedocs.github.io/aks/ 上的示例应用程序


## <a name="oss-enablers"></a>OSS 启用程序 
> [!NOTE]
> 以下解决方案通过开源项目提供，不直接与 Azure 机密计算 (ACC) 或 Microsoft 关联。  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) 是一种轻型来宾 OS，旨在运行具有最低主机要求的单个 Linux 应用程序。 Graphene 可以在隔离的环境中运行应用程序，其优点可与运行完整的 OS 相比，并且它具有良好的工具支持，可将现有的 docker 容器应用程序转换为受 Graphene 防护的容器 (GSC)。

若要开始在 AKS 上使用示例应用程序和部署，请参阅[此文](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) 是适用于 Intel SGX 的、内存安全的多进程库 OS (LibOS)。 它使旧版应用程序能够在 SGX 上运行，只需对源代码进行很少的修改，甚至根本不需要进行修改。 Occlum 以透明方式保护用户工作负荷的机密性，同时可以让用户轻松地直接迁移到现有的 docker 应用程序。

Occlum 支持 AKS 部署。 按照[此处](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)提供的各种示例应用的部署说明进行操作

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) 是机密容器的业务流程框架。 有了它，就可以轻松地在支持 SGX 的 Kubernetes 上运行和缩放机密服务。 Marblerun 负责样板任务，例如验证群集中的服务、管理这些服务的机密，以及在这些服务之间建立 enclave 到 enclave 的 mTLS 连接。 Marblerun 还确保机密容器的群集符合以简单 JSON 定义的清单。 外部客户端可以通过远程证明来验证清单。 

![Marblerun 流](./media/confidential-containers/marblerun-workflow.png)

简而言之，Marblerun 可将单个 enclave 的机密性、完整性和可验证性属性扩展到 Kubernetes 群集。 

Marblerun 支持使用 Graphene、Occlum 和 EGo 创建的机密容器。 [此处](https://www.marblerun.sh/docs/examples/)提供了每个 SDK 的示例。 Marblerun 构建为在 Kubernetes 上与现有的云原生工具一起运行。 它附带了易用的 CLI 和 helm chart。 它为 AKS 上的机密计算节点提供一流支持。 可在[此处](https://www.marblerun.sh/docs/deployment/cloud/)找到有关如何在 AKS 上部署 Marblerun 的信息。

## <a name="confidential-containers-demo"></a>机密容器演示
观看有关机密容器的机密医疗保健演示。 [此处](/azure/architecture/example-scenario/confidential/healthcare-inference)提供示例。 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>联系方式

如果在实现过程中出现问题，或者希望成为赋能者， 请向产品团队发送电子邮件： **acconaks@microsoft.com**

## <a name="reference-links"></a>参考链接

[Microsoft Azure 证明](../attestation/overview.md)

[DCsv2 虚拟机](virtual-machine-solutions.md)

[Azure Kubernetes 服务 (AKS)](../aks/intro-kubernetes.md)
