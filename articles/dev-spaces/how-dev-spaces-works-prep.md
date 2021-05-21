---
title: 如何为 Azure Dev Spaces 准备项目
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍如何为 Azure Dev Spaces 准备项目
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91968151"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>如何为 Azure Dev Spaces 准备项目

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 Dev Spaces 可以为项目生成 Dockerfile 和 Helm 图表。 Dev Spaces 还可以创建并使用用于在 AKS 中部署、运行和调试 Kubernetes 应用程序的配置文件。 所有这些文件都驻留在应用程序的代码中，可以添加到版本控制系统。

本文介绍了准备项目以使用 Dev Spaces 在 AKS 中运行时将发生的情况。

## <a name="prepare-your-code"></a>准备代码

若要在开发空间中运行应用程序，需要对其进行容器化，并需要定义应如何将其部署到 Kubernetes。 若要容器化应用程序，你需要一个 Dockerfile。 若要定义将应用程序部署到 Kubernetes 的方式，你需要一个 [Helm 图表](https://docs.helm.sh/)。 为协助创建应用程序的 Dockerfile 和 Helm 图表，客户端工具提供了 `prep` 命令：

```cmd
azds prep --enable-ingress
```

`prep` 命令将查看项目中的文件，并尝试创建 Dockerfile 和 Helm 图表，以便在 Kubernetes 中运行应用程序。 目前，`prep` 命令将使用以下语言生成 Dockerfile 和 Helm 图表：

* Java
* Node.js
* .NET Core

必须从包含源代码的目录中运行 `prep` 命令。 从正确的目录中运行 `prep` 命令，可让客户端工具识别语言并创建相应的 Dockerfile 来容器化应用程序。 还可以从包含 Java 项目的 pom.xml 文件的目录中运行 `prep` 命令。

如果从不包含源代码的目录中运行 `prep` 命令，则客户端工具不会生成 Dockerfile。 它还会显示一个错误，指出：由于语言不受支持，无法生成 Dockerfile。 如果客户端工具无法识别项目类型，也会发生此错误。

运行 `prep` 命令时，可以选择指定 `--enable-ingress` 标志。 此标志告知控制器为此服务创建可通过 Internet 访问的终结点。 如果未指定此标志，则只能从群集内部或使用客户端工具创建的 localhost 隧道访问该服务。 可以在运行 `prep` 命令后通过更新生成的 Helm 图表来启用或禁用此行为。

`prep` 命令不会替换项目中的任何现有 Dockerfile 或 Helm 图表。 如果现有 Dockerfile 或 Helm 图表使用的命名约定与 `prep` 命令生成的文件相同，则 `prep` 命令将跳过生成这些文件。 否则，`prep` 命令将随现有文件一起生成自己的 Dockerfile 或 Helm 图表。

> [!IMPORTANT]
> Azure Dev Spaces 使用项目的 Dockerfile 和 Helm 图表来生成和运行代码，但是如果要更改项目的生成和运行方式，则可以修改这些文件。

`prep` 命令还将在项目的根目录中生成 `azds.yaml` 文件。 Azure Dev Spaces 使用此文件来生成、安装、配置和运行应用程序。 此配置文件列出 Dockerfile 和 Helm 图表的位置，还在这些项目之上提供其他配置。

下面是使用 [.Net Core 示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)创建的示例 azds.yaml 文件：

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`prep` 命令生成的 `azds.yaml` 文件旨在适用于简单的单一项目开发方案。 如果特定项目增加了复杂性，则在运行 `prep` 命令后，可能需要更新此文件。 例如，你的项目可能需要根据你的开发或调试需求对生成或启动过程进行一些更改。 你的项目中还可能有多个应用程序，这需要多个生成过程或不同的生成内容。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在开发空间中运行代码，请参阅[如何使用 Azure Dev Spaces 运行代码][how-it-works-up]。

[how-it-works-up]: how-dev-spaces-works-up.md