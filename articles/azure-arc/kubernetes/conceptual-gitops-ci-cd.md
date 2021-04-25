---
title: 使用 GitOps 的 CI/CD 工作流 - 已启用 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: 本文提供使用 GitOps 的 CI/CD 工作流的概念性概述
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes 服务, 容器, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121773"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>使用 GitOps 的 CI/CD 工作流 - 已启用 Azure Arc 的 Kubernetes

新式 Kubernetes 部署容纳了多个应用程序、群集和环境。 使用 GitOps 可以更轻松地管理这些复杂的设置，并通过 Git 以声明方式跟踪 Kubernetes 环境的所需状态。 使用常见的 Git 工具跟踪群集状态，可以增强问责机制、简化故障调查，并启用自动化来管理环境。

本概念性概述将 GitOps 作为使用 Azure Arc、Azure Repos 和 Azure Pipelines 的整个应用程序更改生命周期中采用的现实方式进行介绍。 跳转到对 GitOps 所控制的 Kubernetes 环境中的单个应用程序进行更改的[示例](#example-workflow)。

## <a name="architecture"></a>体系结构

考虑要部署到一个或多个 Kubernetes 环境的某个应用程序。

![GitOps CI/CD 体系结构](./media/gitops-arch.png)

### <a name="application-repo"></a>应用程序存储库
应用程序存储库包含开发人员在运行其内部循环期间处理的应用程序代码。 应用程序的部署模板以某种常规格式（例如 Helm 或 Kustomize）驻留在此存储库中。 不会存储环境特定的值。 对此存储库进行更改会调用一个启动部署过程的 PR 或 CI 管道。
### <a name="container-registry"></a>容器注册表
容器注册表保存 Kubernetes 环境中使用的所有第一方和第三方映像。 使用用户可读的标记以及用于生成映像的 Git 提交内容来标记第一方应用程序映像。 缓存第三方映像以提高安全性、速度和复原能力。 设置计划以便及时测试和集成安全更新。 有关详细信息，请参阅[通过 ACR 使用和维护公共内容](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content)指南中的示例。
### <a name="pr-pipeline"></a>PR 管道
成功运行 PR 管道时，对应用程序存储库的 PR 将受到门限控制。 此管道运行基本质量门，例如对应用程序代码运行 Lint 检查和单元测试。 该管道将测试应用程序，并通过 Lint 检查用于部署到 Kubernetes 环境的 Dockerfile 和 Helm 模板。 应生成并测试 Docker 映像，但不要推送映像。 使管道持续时间保持相对较短，以便快速迭代。
### <a name="ci-pipeline"></a>CI 管道
应用程序 CI 管道运行所有 PR 管道步骤，并扩展测试和部署检查。 可对每项提交内容或者按照固定的频率对一组提交内容运行该管道。 在此阶段，将执行对于 PR 管道而言时间太长的应用程序测试。 生成准备部署的 Docker 映像后，将映像推送到容器注册表。 可以使用一组测试值来通过 Lint 检查替换的模板。 此时应当对在服务运行时使用的映像进行 Lint 检查、生成和测试。 具体而言，在 CI 生成中，将发布在准备部署时供 CD 步骤使用的项目。
### <a name="flux"></a>Flux
Flux 是在每个群集中运行的服务，负责维护所需状态。 该服务在 GitOps 存储库中频繁轮询对其群集所做的更改，并应用这些更改。
### <a name="cd-pipeline"></a>CD 管道
CI 生成成功时会自动触发 CD 管道。 该管道使用先前发布的模板，替代环境值，并创建对 GitOps 存储库发出的 PR，以请求对一个或多个 Kubernetes 群集的所需状态做出更改。 群集管理员会评审状态更改 PR，并批准合并到 GitOps 存储库。 然后，该管道等待 PR 完成，接下来 Flux 便可拾取状态更改。
### <a name="gitops-repo"></a>GitOps 存储库
GitOps 存储库代表各个群集中所有环境的当前所需状态。 对此存储库所做的任何更改将由每个群集中的 Flux 服务拾取，并予以部署。 将创建包含所需状态的更改的 PR，并评审、合并 PR。 这些 PR 包含对部署模板以及生成的 Kubernetes 清单所做的更改。 使用在低级别呈现的清单可以更仔细地检查在模板级别通常看不到的更改。
### <a name="kubernetes-clusters"></a>Kubernetes 群集
至少有一个已启用 Azure Arc 的 Kubernetes 群集为应用程序所需的不同环境提供服务。 例如，一个群集可以通过不同的命名空间为开发和 QA 环境提供服务。 另一个群集可以提供更简便的环境隔离和更精细的控制。
## <a name="example-workflow"></a>示例工作流
作为应用程序开发人员，Alice 需要：
* 编写应用程序代码。
* 确定如何在 Docker 容器中运行应用程序。
* 定义用于在 Kubernetes 群集中运行容器和相关服务的模板。

虽然 Alice 知道应用程序需要能够在多个环境中运行，但她并不知道每个环境的特定设置。

假设 Alice 想要做出某项应用程序更改，此项更改会改变应用程序部署模板中使用的 Docker 映像。

1. Alice 更改了部署模板，将其推送到了应用程序存储库中的远程分支，并创建了 PR 以供评审。
2. Alice 请求她的团队评审更改。
    * PR 管道运行验证。
    * 管道运行成功后，团队会进行签核，并且更改会被合并。
3. CI 管道验证 Alice 的更改并成功完成。
    * 此更改可以安全地部署到群集，并且项目将保存到 CI 管道运行。
4. Alice 的更改将会合并，并触发 CD 管道。
    * CD 管道拾取 Alice 的 CI 管道运行存储的项目。
    * CD 管道将模板替换为环境特定的值，并暂存针对 GitOps 存储库中现有群集状态的任何更改。
    * CD 管道创建要对 GitOps 存储库发出的 PR，其中包含对群集状态的所需更改。
5. Alice 的团队评审并批准她的 PR。
    * 更改将合并到与环境对应的目标分支中。
6. 在几分钟内，Flux 将会注意到 GitOps 存储库中发生了更改，并拉取 Alice 的更改。
    * 由于 Docker 映像的更改，应用程序 Pod 需要更新。
    * Flux 将应用对群集的更改。
7. Alice 测试应用程序终结点，以验证部署是否成功完成。
   > [!NOTE]
   > 对于其他面向部署的环境，CD 管道将通过为下一个环境创建 PR 并重复步骤 4-7 来不断迭代。 该过程可能需要对风险更高的部署或环境（例如安全相关的更改或生产环境）进行额外的批准。
8.  所有环境收到成功的部署后，管道即告完成。

## <a name="next-steps"></a>后续步骤
详细了解如何在群集与 Git 存储库之间创建连接，用作[已启用 Azure Arc 的 Kubernetes 中的配置资源](./conceptual-configurations.md)
