---
title: Azure Dev Spaces 工作原理
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 描述支持 Azure Dev Spaces 的进程
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963697"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces 工作原理

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

开发 Kubernetes 应用程序可能非常困难。 需要 Docker 和 Kubernetes 配置文件。 需要确定如何在本地测试应用程序并与其他依赖服务进行交互。 可能需要与开发者团队合作同时开发和测试多个服务。

Azure Dev Spaces 为用户提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并与团队协作。 本文介绍了 Azure Dev Spaces 可以执行的操作及其工作方式。

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>快速循环访问和调试 Kubernetes 应用程序

Azure Dev Spaces 降低了在 AKS 群集的上下文中开发、测试和迭代 Kubernetes 应用程序的工作量。 由于工作量如此降低，开发人员可以专注于应用程序的业务逻辑，而不是将其服务配置为在 Kubernetes 中运行。

### <a name="bridge-to-kubernetes"></a>Kubernetes 桥接

通过桥接到 Kubernetes，用户可以将开发计算机连接到 Kubernetes 群集，这样就可以在开发计算机上运行和调试代码，就像在群集上运行一样。 Bridge to Kubernetes 可重定向已连接群集之间的流量，方式在群集上运行一个 pod，其可充当远程代理来重定向开发计算机和群集之间的流量。 此流量重定向使得开发计算机上的代码能够与群集中运行的服务进行通信，就像它们位于同一个群集中一样。 有关将开发计算机连接到 Kubernetes 群集的详细信息，请参阅“[Bridge to Kubernetes 的工作方式][how-it-works-bridge-to-kubernetes]”。

### <a name="run-your-code-in-aks"></a>在 AKS 中运行代码

除了在开发计算机与 AKS 群集之间重定向流量外，使用 Azure Dev Spaces，用户还可以直接配置并快速在 AKS 中运行代码。 Azure Dev spaces 会通过 Visual Studio、Visual Studio Code 或 Azure Dev Spaces CLI 将代码上传到群集，然后生成并运行。 Azure Dev spaces 还可以智能地同步代码更改，并重启服务，以体现所需的更改。 运行代码时，生成日志和 HTTP 跟踪会流式传输回客户端，以便用户监视进度并诊断任何问题。 用户还可以使用 Azure Dev Spaces 将 Visual Studio 和 Visual Studio Code 中的调试程序附加到 Java、Node.js 和 .NET Core 服务中。 有关详细信息，请参阅“[准备 Azure Dev Spaces 项目的方式][how-it-works-prep]”、“[使用 Azure Dev Spaces 运行代码的方式][how-it-works-up]”，以及“[使用 Azure Dev Spaces 远程调试代码的方式][how-it-works-remote-debugging]”。

## <a name="team-development"></a>团队开发

归功于 Azure Dev Spaces ，团队可以在同一 AKS 群集上高效地使用应用程序，而不会中断。

### <a name="intelligent-routing-between-dev-spaces"></a>dev spaces 之间的智能路由

使用 Azure Dev Spaces，团队可以共享运行云本机应用程序的单个 AKS 群集，并创建独立的dev spaces，团队无需干扰其他 dev spaces 即可进行开发、测试和调试。 应用程序的基线版本在根 dev space 中运行。 然后，团队成员会根据用于开发、测试和调试应用程序更改的根空间创建独立的子 dev space。 通过 Dev Spaces 中的路由功能，子 dev spaces 可以在子 dev space 和父 dev space 中运行的服务之间路由请求。 使用此路由，开发者可在从父空间重用依赖服务时运行其自己的服务版本。 每个子空间都有其自己的唯一 URL，供其他人共享和访问以进行协作。 有关 Azure Dev Spaces 中路由工作方式的更多信息，请参阅“[Azure Dev Spaces 中的路由工作方式][how-it-works-routing]”。

### <a name="live-testing-an-open-pull-request"></a>实时测试开放式拉取请求

用户还可以将 GitHub Actions 与 Azure Dev Spaces 一起使用，以便在合并之前在群集中直接测试对拉取请求中应用程序所做的更改。 Azure Dev Spaces 可以将应用程序的审阅版本自动部署到群集，以便作者和其他团队成员查看整个应用程序上下文中的更改。 使用 Azure Dev Spaces 的路由功能，还会将此审阅版本的应用程序部署到群集中，而不会影响其他 dev spaces。 所有这些功能都允许用户放心地批准及合并请求。 若要查看 GitHub Actions 和 Azure Dev Spaces 的示例，请参阅“[GitHub Actions & Azure Kubernetes Service][pr-flow]”。

## <a name="next-steps"></a>后续步骤

若要开始将本地开发计算机连接到 AKS 群集，请参阅“[将开发计算机连接到 AKS 群集][connect]”。


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development