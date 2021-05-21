---
title: 使用 Azure Dev Spaces 运行代码的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍使用 Azure Dev Spaces 在 Azure Kubernetes 服务上运行代码的流程
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014425"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>使用 Azure Dev Spaces 运行代码的工作原理

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 一旦[项目准备好在开发空间运行][how-it-works-prep]，就可以使用 Dev Spaces 在 AKS 群集中生成和运行项目。

本文介绍使用 Dev Spaces 在 AKS 中运行代码会发生什么情况。

## <a name="run-your-code"></a>运行代码

若要在开发空间中运行代码，请在 `azds.yaml` 文件所在的目录中发出 `up` 命令：

```cmd
azds up
```

`up` 命令可将生成和运行项目所需的应用程序源文件和其他项目上传到开发空间。 在那里，开发空间中的控制器将：

1. 创建 Kubernetes 对象以部署应用程序。
1. 为应用程序生成容器。
1. 将应用程序部署到开发空间。
1. 为应用程序终结点（如果已配置）创建可公开访问的 DNS 名称。
1. 使用端口转发通过 http://localhost 提供对应用程序终结点的访问。
1. 将 stdout 和 stderr 转发到客户端工具。


## <a name="starting-a-service"></a>启动服务

在开发空间中启动服务时，客户端工具和控制器将协同工作，以同步源文件、创建容器和 Kubernetes 对象以及运行应用程序。

下面在更精细的层面上介绍了运行 `azds up` 时发生的情况：

1. [文件从用户计算机同步][sync-section]到用户 AKS 群集独有的 Azure 文件存储。 系统将上传源代码、Helm 图表和配置文件。
1. 控制器创建一个启动新会话的请求。 该请求包含多个属性，包括唯一 ID、空间名称、源代码路径和调试标志。
1. 控制器将 Helm 图表中的 $(tag) 占位符替换为唯一会话 ID，并为你的服务安装 Helm 图表。 通过向 Helm 图表添加对唯一会话 ID 的引用，可以将为此特定会话部署到 AKS 群集的容器重新绑定到会话请求和关联信息。
1. 在安装 Helm 图表期间，Kubernetes Webhook 许可服务器会向应用程序的 Pod 添加其他容器，以进行检测和访问项目的源代码。 它会添加 devspaces-proxy 和 devspaces-proxy-init 容器，以提供 HTTP 跟踪和空间路由。 还会添加 devspaces-build 容器，以使 Pod 可以访问 Docker 实例和用于生成应用程序容器的项目源代码。
1. 启动应用程序的 Pod 时，系统会使用 devspaces-build 容器和 devspaces-proxy-init 容器来生成应用程序容器。 然后启动应用程序容器和 devspaces-proxy 容器。
1. 启动应用程序容器后，客户端功能使用 Kubernetes 端口转发功能通过 http://localhost 提供对应用程序的 HTTP 访问。 此端口转发会将开发计算机连接到开发空间中的服务。
1. 如果 Pod 中的所有容器都已启动，则表示服务正在运行。 此时，客户端功能开始流式传输 HTTP 跟踪、stdout 和 stderr。 这些信息由客户端功能向开发人员显示。

## <a name="updating-a-running-service"></a>更新正在运行的服务

当服务正在运行时，如果有任何项目源文件发生更改，Azure Dev Spaces 都可以更新该服务。 Dev Spaces 还可以根据更改的文件类型，以不同的方式处理服务更新。 Dev Spaces 可以通过三种方式更新正在运行的服务：

* 直接更新文件
* 在正在运行的应用程序的容器中重新生成并重启应用程序的进程
* 重新生成并重新部署应用程序的容器

![Azure Dev Spaces 文件同步](media/how-dev-spaces-works/file-sync.svg)

某些属于静态资产的项目文件（例如 html、css 和 cshtml 文件）可以直接在应用程序的容器中更新，而无需重启任何内容。 如果静态资产发生更改，新文件将同步到开发空间，然后由正在运行的容器使用。

你可以通过在正在运行的容器中重启应用程序进程，来应用对源代码或应用程序配置文件等文件的更改。 这些文件同步后，系统会使用 devhostagent 进程在正在运行的容器中重启应用程序进程。 最初创建应用程序容器时，控制器使用名为 devhostagent 的不同进程替换应用程序的启动命令。 然后，应用程序的实际进程作为子进程在 devhostagent 下运行，其输出通过 devhostagent 的输出进行传输。 devhostagent 进程也是 Dev Spaces 的一部分，可以代表 Dev Spaces 在正在运行的容器中执行命令。 执行重启时，devhostagent 会：

* 停止与应用程序关联的当前进程
* 重新生成应用程序
* 重启与应用程序关联的进程

devhostagent 执行上述步骤的方式[在 `azds.yaml` 中配置][azds-yaml-section]。

更新项目文件（例如 Dockerfile、csproj 文件或 Helm 图表的任何部分）需要重新生成并重新部署应用程序的容器。 当其中一个文件同步到开发空间时，控制器将运行 [helm upgrade][helm-upgrade]命令，并且将重新生成并重新部署应用程序的容器。

## <a name="file-synchronization"></a>文件同步

首次在开发空间中启动应用程序时，系统会上传应用程序的所有源文件。 当应用程序正在运行时，以及在以后的重启中，系统仅上传已更改的文件。 可使用两个文件来协调此过程：客户端文件和控制器端文件。

客户端文件存储在临时目录中，并根据你在 Dev Spaces 中运行的项目目录的哈希进行命名。 例如，在 Windows 上，你的项目会有一个诸如 Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog 之类的文件。 在 Linux 上，客户端文件存储在 /tmp 目录中。 在 macOS 上，你可以通过运行 `echo $TMPDIR` 命令找到该目录。

此文件采用 JSON 格式，其中包含：

* 与开发空间同步的每个项目文件的条目
* 同步 ID
* 上次同步操作的时间戳

每个项目文件条目均包含该文件的路径及其时间戳。

控制器端文件存储在 AKS 群集上。 它包含同步 ID 和上次同步的时间戳。

当客户端文件和控制器端文件之间的同步时间戳不匹配时，将发生同步。 在同步期间，客户端工具将循环访问客户端文件中的文件条目。 如果文件的时间戳晚于同步时间戳，该文件将同步到开发空间。 同步完成后，客户端文件和控制器端文件上的同步时间戳都会更新。

如果未提供客户端文件，则将同步所有项目文件。 此行为使你可以通过删除客户端文件来强制完全同步。

## <a name="how-running-your-code-is-configured"></a>如何配置代码运行

Azure Dev Spaces 使用 `azds.yaml` 文件来安装和配置服务。 控制器使用 `azds.yaml` 文件中的 `install` 属性来安装 Helm 图表和创建 Kubernetes 对象：

```yaml
...
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
...
```

默认情况下，`prep` 命令将生成 Helm 图表。 它还将 install.chart 属性设置为 Helm 图表的目录。 如果要在其他位置使用 Helm 图表，则可以将此属性更新为使用该位置。

安装 Helm 图表时，Azure Dev Spaces 提供一种替代 Helm 图表中的值的方法。 Helm 图表的默认值在 `charts/APP_NAME/values.yaml` 中。

使用 install.values 属性，你可以列出一个或多个文件，这些文件定义了要在 Helm 图表中替换的值。 例如，如果在开发空间中运行应用程序时特别想要某个主机名或数据库配置，则可以使用此替代功能。 你还可以在任何文件名的末尾添加 ?， 以将其设置为可选。

install.set 属性允许你配置一个或多个要在 Helm 图表中替换的值。 install.set 中配置的任何值都将替代 install.values 中列出的文件中配置的值。 install.set 下的属性依赖于 Helm 图表中的值，并且可能会因生成的 Helm 图表而异。

在上面的示例中，install.set.replicaCount 属性告诉控制器要在开发空间中运行的应用程序实例数。 你可以酌情增加此值，但它会对将调试程序附加到应用程序 Pod 有影响。 有关详细信息，请参阅[故障排除文章][troubleshooting]。

在生成的 Helm 图表中，容器映像设置为 {{ .Values.image.repository }}:{{ .Values.image.tag }}。 默认情况下，`azds.yaml` 文件将 install.set.image.tag 属性定义为 $(tag)，该属性用作 {{ .Values.image.tag }} 的值。 通过以这种方式设置 install.set.image.tag 属性，它允许在运行 Azure Dev Spaces 时以不同的方式标记应用程序的容器映像。 在这种特定情况下，映像标记为 \<value from image.repository>:$(tag)。 你必须使用 $(tag) 变量作为 install.set.image.tag 的值，Dev Spaces 才能在 AKS 群集中识别并找到容器。

在上面的示例中，`azds.yaml` 定义了 install.set.ingress.hosts。 install.set.ingress.hosts 属性定义公共终结点的主机名格式。 此属性还使用 $(spacePrefix)、$(rootSpacePrefix) 和 $(hostSuffix)，它们是控制器提供的值。

$(spacePrefix) 是子开发空间的名称，其格式为 SPACENAME.s。 $(rootSpacePrefix) 是父空间的名称。 例如，如果 azureuser 是 default 的子空间，则 $(rootSpacePrefix) 的值为 default，而 $(spacePrefix) 的值为 azureuser.s。 如果该空间不是子空间，则 $(spacePrefix) 为空白。 例如，如果 default 空间没有父空间，则 $(rootSpacePrefix) 的值为 default，而 $(spacePrefix) 的值为空白。 $(hostSuffix) 是一个 DNS 后缀，指向在 AKS 群集中运行的 Azure Dev Spaces 入口控制器。 此 DNS 后缀对应于通配符 DNS 条目，例如 \*.RANDOM_VALUE.eus.azds.io，该条目是在将 Azure Dev Spaces 控制器添加到 AKS 群集时创建的。

在上面的 `azds.yaml` 文件中，你还可以更新 install.set.ingress.hosts ，以更改应用程序的主机名。 例如，如果要将应用程序的主机名从 $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix) 简化为 $(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)。

若要为应用程序生成容器，控制器将使用 `azds.yaml` 配置文件的以下各节：

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

控制器使用 Dockerfile 来生成和运行应用程序。

build.context 属性列出 Dockerfile 所在的目录。 build.dockerfile 属性定义用于生成应用程序生产版本的 Dockerfile 的名称。 configurations.develop.build.dockerfile 属性为应用程序的开发版本配置 Dockerfile 的名称。

通过为开发和生产使用不同的 Dockerfile，你可以在开发期间启用某些项，而为生产部署禁用这些项。 例如，你可以在开发过程中启用调试或更详细的日志记录，而在生产环境中禁用它。 如果 Dockerfile 的名称不同或位于不同的位置，你也可以更新这些属性。

为了帮助你在开发过程中快速迭代，Azure Dev Spaces 将同步本地项目中的更改并增量更新应用程序。 `azds.yaml` 配置文件中的以下节用于配置同步和更新：

```yaml
...
configurations:
  develop:
    ...
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
...
```

configurations.develop.container.sync 属性中列出了将同步更改的文件和目录。 这些目录最初在运行 `up` 命令以及检测到更改时同步。 如果你希望将其他目录同步到开发空间，则可以更改此属性。

configurations.develop.container.iterate.buildCommands 属性指定如何在开发方案中生成应用程序。 configurations.develop.container.command 属性提供用于在开发方案中运行应用程序的命令。 如果要在开发过程中使用其他生成或运行时标志或参数，可能需要更新这两个属性之一。

configurations.develop.container.iterate.processesToKill 列出要终止以停止应用程序的进程。 如果要在开发过程中更改应用程序的重启行为，可能需要更新此属性。 例如，如果更新了 configurations.develop.container.iterate.buildCommands 或 configurations.develop.container.command 属性，以更改应用程序的生成或启动方式，则可能需要更改要停止的进程。

使用 `azds prep` 命令准备代码时，可以选择添加 `--enable-ingress` 标志。 添加 `--enable-ingress` 标志会为应用程序创建一个可公开访问的 URL。 如果省略此标志，则只能在群集中或使用 localhost 隧道访问应用程序。 运行 `azds prep` 命令后，可以通过修改 `charts/APPNAME/values.yaml` 中的 ingress.enabled 属性来更改此设置：

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>后续步骤

若要详细了解网络以及如何在 Azure Dev Spaces 中路由请求，请参阅[路由如何配合 Azure Dev Spaces 工作][how-it-works-routing]。

若要详细了解如何使用 Kubernetes 快速迭代和开发，请参阅 [Kubernetes 桥接的工作原理][how-it-works-bridge-to-kubernetes]和[使用 Azure Dev Spaces 远程调试代码的工作原理][how-it-works-remote-debugging]。


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md