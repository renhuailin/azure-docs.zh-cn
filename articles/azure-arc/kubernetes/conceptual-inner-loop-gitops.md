---
title: 采用 GitOps 的团队的内部循环开发人员体验
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: conceptual
author: sudivate
ms.author: sudivate
description: '本文提供采用 GitOps 的团队的内部循环开发人员体验的概念概述 '
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes 服务, 容器, CI, CD, Azure DevOps, 内部循环, 开发体验
ms.openlocfilehash: 3c0e8041a31c506c926315381741afd75bb8a806
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598554"
---
# <a name="inner-loop-developer-experience-for-teams-adopting-gitops"></a>采用 GitOps 的团队的内部循环开发人员体验

本文介绍已建立的内部循环如何提高开发人员工作效率，并帮助采用 GitOps 的团队从内部开发循环无缝转换到外部循环。

## <a name="inner-dev-loop-frameworks"></a>内部开发循环框架

生成和部署容器可能会降低内部开发体验并影响团队工作效率。 云原生开发团队将受益于可靠的内部开发循环框架。 内部开发循环框架有助于编写代码、生成和调试的迭代过程。

内部开发循环框架功能包括：

 
- 自动执行重复步骤，例如生成代码、容器和部署到目标群集。 
- 轻松使用远程和本地群集，并支持混合安装的本地隧道调试。
- 能够配置自定义流，提高团队工作效率。
- 允许处理微服务依赖项。 
- 热重载、端口转发、日志和终端访问。 



根据服务的成熟度和复杂性，开发团队将确定他们将使用哪个群集设置来加速内部开发循环： 

* 完全本地
* 完全远程
* 混合 


幸运的是，有许多框架支持列出的功能。 Microsoft 为本地隧道调试提供了 Bridge to Kubernetes，还有类似的市场产品/服务，如 DevSpace、Scaffold 和 Tilt 等等。

> [!NOTE]
> 请勿将市场服务/产品 [DevSpace](https://github.com/loft-sh/devspace) 和 Microsoft 之前的 DevSpace（现为 [Bridge to Kubernetes](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)）混淆。


## <a name="inner-loop-to-outer-loop-transition"></a>内部循环到外部循环的转换 

评估并选择内部循环开发框架后，生成内部循环到外部循环的无缝转换。

如[使用 GitOps 的 CI/CD 工作流](conceptual-gitops-ci-cd.md)文章的示例所述，应用程序开发人员在应用程序存储库中处理应用程序代码。 此应用程序存储库还保存高级部署 Helm 和/或 Kustomize 模板。 CI\CD 管道：

* 从高级别模板生成低级别清单，并添加特定于环境的值
* 创建一个拉取请求，该请求将低级别清单与保留特定环境的所需状态的 GitOps 存储库合并。 

使用开发人员的本地配置值，可在本地为内部开发循环生成类似的低级别清单。 应用程序开发人员可对代码更改进行迭代，并使用低级别清单来部署和调试应用程序。 使用开发人员的本地配置，可将低级别清单的生成集成到内部循环工作流中。 大多数内部循环框架都允许通过借助自定义插件进行扩展或基于挂钩注入脚本调用来配置自定义流。 

## <a name="example-inner-loop-workflow-built-with-devspace-framework"></a>使用 DevSpace 框架生成的内部循环工作流示例


### <a name="diagram-a-inner-loop-flow"></a>关系图 A：内部循环流
:::image type="content" source="media/dev-inner-loop.png" alt-text="DevSpace 的内部循环流关系图。":::

### <a name="diagram-b-inner-loop-to-outer-loop-transition"></a>关系图 B：内部循环到外部循环的转换
:::image type="content" source="media/inner-loop-to-outer-loop.png" alt-text="内部循环到外部循环的转换关系图。" :::


## <a name="example-workflow"></a>示例工作流
作为应用程序开发人员，Alice 需要：
- 创作 devspace.yaml 来配置内部循环。
- 使用内部循环编写和测试应用程序代码以提高效率。
- 使用外部循环部署到暂存环境或生产环境。


假设 Alice 想要在本地或远程群集中更新、运行和调试应用程序。

1. Alice 更新 .env 文件中表示的开发环境的本地配置。
1. Alice 运行 `devspace use context` 并选择 Kubernetes 群集上下文。
1.  Alice 通过运行 `devspace use namespace <namespace_name>` 来选择要使用的命名空间。
1.  Alice 通过运行 `devspace dev` 对应用程序代码的更改进行迭代，将应用程序部署到目标群集并对其进行调试。
1. 运行 `devspace dev` 会基于 Alice 的本地配置生成低级别清单并部署应用程序。 这些低级别清单在 devspace.yaml 中配置有 devspace 挂钩
1. Alice 无需在每次进行代码更改时都重新生成容器，因为 DevSpace 将启用热重载，使用文件同步在容器中复制她的最新更改。
1. 运行 `devspace dev` 还将部署在 devspace.yaml 中配置的所有依赖项，如后端依赖项到前端依赖项。 
1. Alice 通过经 devspace.yaml 配置的转发访问应用程序，以测试她的更改。
1. Alice 完成更改后，可以通过运行 `devspace purge` 来清除部署，并创建新的拉取请求，以将更改合并到应用程序存储库的开发分支。

> [!NOTE]
> 在此 [GitHub 存储库](https://github.com/Azure/arc-cicd-demo-src)中查找上述工作流的示例代码


## <a name="next-steps"></a>后续步骤
详细了解如何在群集与 Git 存储库之间创建连接，用作[已启用 Azure Arc 的 Kubernetes 中的配置资源](./conceptual-configurations.md)