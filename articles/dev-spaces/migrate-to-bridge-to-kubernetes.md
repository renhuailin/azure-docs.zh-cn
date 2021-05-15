---
title: 迁移到 Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: 说明从 Azure Dev Spaces 迁移到 Bridge to Kubernetes 的过程
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Bridge to Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888515"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>迁移到 Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces 将于 2023 年 10 月 31 日停用。 客户应转向使用客户端开发者工具 Bridge to Kubernetes。
>
> Azure Dev Spaces 的目的是简化用户在 Kubernetes 上的开发工作。 Azure Dev Spaces 方法的一大缺点是，对用户造成了额外的负担，需要了解 Docker 和 Kubernetes 配置以及 Kubernetes 部署概念。 随着时间的推移，Azure Dev Spaces 方法也没有有效地降低 Kubernetes 上的内部循环开发速度，这是显而易见的。 Bridge to Kubernetes 可有效降低内部循环开发速度，并避免对用户造成不必要的负担。
>
> 核心任务保持不变：打造在更大应用程序的上下文中开发、测试和调试微服务代码的最佳体验。

Bridge to Kubernetes 为使用 Azure Dev Spaces 的许多开发方案提供更轻量的替代方法。 Bridge to Kubernetes 是使用 [Visual Studio][vs]  和  [Visual Studio Code][vsc] 中的扩展的仅限客户端体验。  

Bridge to Kubernetes 通过允许已建立的 Kubernetes 应用程序包括在本地开发工作站上运行的服务，帮助改善你的开发体验。 与 Dev Spaces 不同，Bridge to Kubernetes 通过规避创建 Docker 和 Kubernetes 配置的需要，可减少内部循环复杂性，从而使开发人员能够完全专注于其微服务代码的业务逻辑。

Bridge to Kubernetes 有助于循环访问开发计算机上运行的代码，同时使用 Kubernetes 环境中的依赖项和现有配置。 相比之下，Azure Dev Spaces 先将微服务部署到 Kubernetes 环境中，然后才能远程调试服务并循环访问代码。

本文将 Azure Dev Spaces 和 Bridge to Kubernetes 进行比较，并提供从 Azure Dev Spaces 迁移到 Bridge to Kubernetes 的说明。

## <a name="development-approaches"></a>开发方法

![开发方法](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces 帮助 Kubernetes 开发人员使用直接在其 AKS 群集中运行的代码，从而无需与已部署环境不相似的本地环境。 这种方法改进了开发的某些方面，但也导致必须先学习和维护其他概念（如 Docker、Kubernetes 和 Helm），然后才能开始使用 Azure Dev Spaces。

Bridge to Kubernetes 使开发人员能够直接在其开发计算机上工作，同时与群集的其余部分进行交互。 此方法利用了直接在开发计算机上运行代码的熟悉性和速度，同时共享群集提供的依赖项和环境。 此方法还利用了在 Kubernetes 中运行附带的保真度和缩放。

## <a name="feature-comparison"></a>功能对比

Azure Dev Spaces 和 Bridge to Kubernetes 具有类似的功能，但也在多个方面有所不同：

| 要求  | Azure Dev Spaces  | Kubernetes 桥接  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes 服务 | 在 15 个 Azure 区域 | 任何 AKS 服务区域    |
| **安全性** |
| 群集上所需的安全访问  | AKS 群集参与者  | Kubernetes RBAC - 部署更新   |
| 开发计算机上所需的安全访问  | 不适用  | 本地管理员/sudo   |
| **可用性** |
| 独立于 Kubernetes 和 Docker 项目  | 否  | 是   |
| 自动回滚更改，调试后  | 否  | 是   |
| **支持的客户端工具** |
| 使用 Visual Studio 2019  | 是  | 是   |
| 使用 Visual Studio Code  | 是  | 是   |
| 使用 CLI  | 是  | 否   |
| **操作系统兼容性** |
| 在 Windows 10 上工作  | 是  | 是  |
| 在 Linux 上工作  | 是  | 是  |
| 在 macOS 上工作  | 是  | 是  |
| **功能** |
| 开发人员隔离或团队开发  | 是  | 是  |
| 有选择地覆盖环境变量  | 否  | 是  |
| 创建 Dockerfile 和 Helm 图表  | 是  | 否  |
| 将代码永久部署到 Kubernetes  | 是  | 否  |
| 在 Kubernetes pod 中进行远程调试  | 是  | 否  |
| 本地调试，连接到 Kubernetes  | 否  | 是  |
| 在同一工作站上同时调试多个服务  | 是  | 是  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes 内部循环开发

Azure Dev Spaces 和 Bridge to Kubernetes 之间的最大差异在于代码的运行位置。 Azure Dev Spaces 有助于开发和调试微服务代码，但需要在群集中运行该代码。 Bridge to Kubernetes 允许直接在开发计算机上开发和调试微服务代码，同时仍处于在 Kubernetes 中运行的更大应用程序的上下文中。 Bridge to Kubernetes 扩展 Kubernetes 群集的外围，并允许本地进程从 Kubernetes 继承配置。

![内部循环开发](media/migrate-to-btk/btk-graphic-non-isolated.gif)

使用 Bridge to Kubernetes 时，会在开发计算机与群集之间建立网络连接。 在此连接的生存期内，会将代理添加到群集中，以替代将服务请求重定向到开发计算机的 Kubernetes 部署。 断开连接后，应用程序部署将恢复为使用群集上运行的部署的原始版本。 这种方法与 Azure Dev Spaces 的工作原理不同，后者的工作原理是，代码同步到群集，构建，然后运行。 Azure Dev Spaces 也不会回滚代码。

Bridge to Kubernetes 可以灵活地使用在 Kubernetes 中运行的应用程序，不管采用哪种部署方法。 如果使用 CI/CD 构建并运行应用程序，则不管是使用已建立的工具还是自定义脚本，仍可以使用 Bridge to Kubernetes 来开发和调试代码。

> [!TIP]
> 利用  [Microsoft Kubernetes 扩展][kubernetes-extension]，可以通过 Intellisense 快速开发 Kubernetes 清单并帮助搭建 Helm 图表。  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>从 Azure Dev Spaces 过渡到 Bridge to Kubernetes

1. 如果使用的是 Visual Studio，请将 Visual Studio IDE 更新到版本 16.7 或更高版本，并从 [Visual Studio Marketplace][vs-marketplace] 安装 Bridge to Kubernetes 扩展。 如果使用的是 Visual Studio Code，则安装 [Bridge to Kubernetes 扩展][vsc-marketplace]。
1. 使用 Azure 门户或 [Azure Dev Spaces CLI][azds-delete] 禁用 Azure Dev Spaces 控制器。
1. 使用 [Azure Cloud Shell](https://shell.azure.com)。 或者，在安装了 bash 的 Mac、Linux 或 Windows 上，打开 bash shell 提示。 请确保命令行环境中提供以下工具：Azure CLI、docker、kubectl、curl、tar 和 gunzip。
1. 创建一个容器注册表或使用现有容器注册表。 可以使用 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)或使用 [Docker Hub](https://hub.docker.com/) 在 Azure 中创建容器注册表。 使用 Azure Cloud Shell 时，只有 Azure 容器注册表可用于托管 docker 映像。
1. 运行迁移脚本，将 Azure Dev Spaces 资产转换为 Kubernetes 资产。 此脚本会构建一个与 Bridge to Kubernetes 兼容的新映像，将其上传到指定的注册表，然后使用 [Helm](https://helm.sh) 更新包含该映像的群集。 必须提供资源组、AKS 群集的名称和容器注册表。 还有其他命令行选项，如下所示：

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   该脚本支持以下标记：

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. 将“azds.yaml”中的任何自定义项（如环境变量设置）手动迁移到项目的“values.yml”文件中。
1. （可选）删除项目中的 `azds.yaml` 文件。
1. 在已部署的应用程序上配置 Bridge to Kubernetes。 要详细了解如何在 Visual Studio 中使用 Bridge to Kubernetes，请参阅[在 Visual Studio 中使用 Bridge to Kubernetes][use-btk-vs]。 有关 VS Code，请参阅[在 VS Code 中使用 Bridge to Kubernetes][use-btk-vsc]。
1. 使用新创建的 Bridge to Kubernetes 调试/启动配置文件开始调试。
1. 用户可以根据需要再次运行该脚本以重新部署到群集。

## <a name="team-development-in-a-shared-cluster"></a>共享群集中的团队开发

用户还可以将特定于开发人员的路由与 Bridge to Kubernetes 一起使用。 Azure Dev Spaces 团队开发方案使用多个 Kubernetes 命名空间，通过使用父和子命名空间的概念将服务与应用程序的其余部分隔离开来。 Bridge to Kubernetes 提供了相同的功能，但具有改进的性能特征，并在相同的应用程序命名空间内。

Bridge to Kubernetes 和 Azure Dev Spaces 都要求在整个应用程序中存在并传播 HTTP 标头。 如果已将应用程序配置为处理 Azure Dev Spaces 的标头传播，则需要更新该标头。 要从 Azure Dev Spaces 过渡到 Bridge to Kubernetes，请将已配置的标头从“azds-route-as”更新为“kubernetes-route-as”。

## <a name="evaluate-bridge-to-kubernetes"></a>评估 Bridge to Kubernetes

如果要在群集中禁用 Azure Dev Spaces 之前尝试使用 Bridge to Kubernetes，最简单的方法是使用新群集。 如果在同一群集上尝试同时使用 Azure Dev Spaces 和 Bridge to Kubernetes，则使用二者的路由功能时将遇到问题。

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>使用 Visual Studio 评估 Bridge to Kubernetes

1. 将 Visual Studio IDE 更新到版本 16.7 或更高版本，并从 [Visual Studio Marketplace][vs-marketplace] 安装 Bridge to Kubernetes 扩展。
1. 创建新的 AKS 群集并部的应用程序。 还可以使用[示例应用程序][btk-sample-app]。
1. 在已部署的应用程序上配置 Bridge to Kubernetes。 要详细了解如何在 Visual Studio 中使用 Bridge to Kubernetes，请参阅[使用 Bridge to Kubernetes][use-btk-vs]。
1. 使用新创建的 Bridge to Kubernetes 调试配置文件开始在 Visual Studio 中进行调试。

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>使用 Visual Studio Code 评估 Bridge to Kubernetes

1. 安装 [Bridge to Kubernetes 扩展][vsc-marketplace]。
1. 创建新的 AKS 群集并部的应用程序。 还可以使用[示例应用程序][btk-sample-app]。
1. 在已部署的应用程序上配置 Bridge to Kubernetes。 要详细了解如何在 Visual Studio Code 中使用 Bridge to Kubernetes，请参阅[使用 Bridge to Kubernetes][use-btk-vsc]。
1. 使用新创建的 Bridge to Kubernetes 启动配置文件开始在 Visual Studio 中进行调试。

## <a name="next-steps"></a>后续步骤

详细了解 Bridge to Kubernetes 的工作方式。

> [!div class="nextstepaction"]
> [Bridge to Kubernetes 的工作原理][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
