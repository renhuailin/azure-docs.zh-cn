---
title: 使用 GitOps 的 CI/CD 工作流-启用了 Azure Arc 的 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/26/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: 本文提供使用 GitOps 的 CI/CD 工作流的概念性概述
keywords: GitOps、Kubernetes、K8s、Azure、Helm、Arc、AKS、Azure Kubernetes 服务、容器、CI、CD、Azure DevOps
ms.openlocfilehash: 044275db0977a20474aa1451324486ad1750a7f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693014"
---
# <a name="overview"></a>概述

新式 Kubernetes 部署拥有多个应用程序、群集和环境。 借助 GitOps，你可以更轻松地管理这些复杂的设置，以声明方式通过 Git 跟踪 Kubernetes 环境的所需状态。 通过使用常见的 Git 工具来跟踪群集状态，你可以提高责任、促进故障调查，并启用自动化来管理环境。

本文简要概述了如何使用 Azure Arc、Azure Repos 和 Azure Pipelines 在应用程序更改的整个生命周期中使 GitOps 成为现实。 演练从开发人员到 GitOps 控制的 Kubernetes 环境的单个应用程序更改的端到端示例。

## <a name="architecture"></a>体系结构

考虑部署到一个或多个 Kubernetes 环境的应用程序。

![GitOps CI/CD 体系结构](./media/gitops-arch.png)
### <a name="application-repo"></a>应用程序存储库
应用程序存储库包含开发人员在其内部循环中运行的应用程序代码。 应用程序的部署模板在此存储库中以通用形式存在，如 Helm 或 Kustomize。 不存储特定于环境的值。 对此存储库所做的更改将调用开始部署过程的 PR 或 CI 管道。
### <a name="container-registry"></a>容器注册表
容器注册表保存在 Kubernetes 环境中使用的所有第一方和第三方映像。 使用人工可读标记和用于生成映像的 Git 提交标记第一方应用程序映像。 缓存第三方映像的安全性、速度和复原能力。 设置计划以及时测试和集成安全更新。 有关详细信息，请参阅 [ACR 使用和维护公共内容](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) 指南以获取示例。
### <a name="pr-pipeline"></a>PR 管道
将 Pr 到应用程序存储库的成功运行。 此管道运行基本质量入口，如应用程序代码上的 linting 和单元测试。 管道测试用于部署到 Kubernetes 环境的应用程序和 lints Dockerfile 和 Helm 模板。 应生成和测试 Docker 映像，但不能推送。 使管道持续时间相对较短，以允许快速迭代。
### <a name="ci-pipeline"></a>CI 管道
应用程序 CI 管道运行所有 PR 管道步骤，并展开测试和部署检查。 可以为每个提交运行管道，也可以在具有一组提交的定期节奏上运行管道。 在此阶段，对 PR 管道执行太长时间的应用程序测试。 为部署做好准备后，将 Docker 映像推送到容器注册表。 替换的模板可以 linted 一组测试值。 此时应 linted、生成和测试服务运行时中使用的映像。 具体而言，CI 版本中发布了项目，以便在准备部署时使用 CD 步骤。
### <a name="flux"></a>Flux
Flux 是一项在每个群集中运行的服务，负责维护所需状态。 服务会频繁轮询 GitOps 存储库，以获取其群集的更改并应用这些更改。
### <a name="cd-pipeline"></a>CD 管道
成功的 CI 生成会自动触发 CD 管道。 它使用以前发布的模板，替代环境值，并打开到 GitOps 存储库的请求，以请求更改一个或多个 Kubernetes 群集的所需状态。 群集管理员查看状态更改 PR 并批准合并到 GitOps 存储库。 然后，管道等待 PR 完成，这允许 Flux 选取状态更改。
### <a name="gitops-repo"></a>GitOps 存储库
GitOps 存储库表示群集上所有环境的当前所需状态。 对此存储库所做的任何更改都将由每个群集中的 Flux 服务选取，并进行部署。 将创建 Pr，并更改所需状态、查看和合并。 这些 Pr 包含对部署模板和生成的 Kubernetes 清单所做的更改。 低级别呈现的清单通过允许仔细检查在模板级别通常不可见的更改，从而避免了模板替换后出现的任何意外情况。
### <a name="kubernetes-clusters"></a>Kubernetes 群集
一个或多个启用了 Azure Arc 的 Kubernetes 群集提供应用程序所需的不同环境。 例如，单个群集可以通过不同的命名空间为开发和 QA 环境提供服务。 第二个群集可以更轻松地分离环境和更精细的控制。
## <a name="example-workflow"></a>示例工作流
作为应用程序开发人员，Alice：
* 写入应用程序代码。
* 确定如何在 Docker 容器中运行应用程序。
* 定义在 Kubernetes 群集中运行容器和从属服务的模板。

尽管 Alice 知道应用程序需要在多个环境中运行的功能，但她并不知道每个环境的特定设置。

假设 Alice 要进行更改以更改应用程序部署模板中使用的 Docker 映像的应用程序更改。

1. Alice 更改部署模板，将其推送到应用程序存储库上的远程分支，并打开 PR 进行评审。
2. Alice 要求她的团队查看更改。
    * PR 管道运行验证。
    * 成功运行管道后，团队将签署并合并更改。
3. CI 管道验证 Alice 的更改并成功完成。
    * 可以安全地将此更改部署到群集，并将项目保存到 CI 管道运行中。
4. Alice 的更改合并和触发 CD 管道。
    * CD 管道将选取由 Alice 的 CI 管道运行存储的项目。
    * CD 管道将模板替换为特定于环境的值，并在 GitOps 存储库中对现有群集状态进行任何更改。
    * CD 管道将使用所需的群集状态更改创建对 GitOps 存储库的 PR。
5. Alice 的团队审查并批准她的 PR。
    * 更改将合并到与环境相对应的目标分支中。
6. 在几分钟内，Flux 会注意到 GitOps 存储库发生了变化并拉取 Alice 的变化。
    * 由于 Docker 映像发生更改，应用程序 pod 需要更新。
    * Flux 将更改应用到群集。
7. Alice 测试应用程序终结点以验证部署是否已成功完成。
   > [!NOTE]
   > 对于更多面向部署的环境，CD 管道会通过为下一环境创建 PR 并重复步骤4-7 来进行迭代。 此过程需要额外批准 riskier 部署或环境，如安全相关的更改或生产环境。
8.  所有环境都收到成功部署后，管道完成。

## <a name="next-steps"></a>后续步骤
[启用了 Azure Arc 的配置和 GitOps Kubernetes](./conceptual-configurations.md)
