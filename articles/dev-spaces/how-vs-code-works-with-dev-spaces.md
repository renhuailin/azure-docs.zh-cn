---
title: Visual Studio Code 如何与 Azure Dev Spaces 配合使用
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: 了解 Visual Studio Code 和 Azure Dev Spaces 如何帮助调试和快速循环访问 Kubernetes 应用程序
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960739"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code 如何与 Azure Dev Spaces 配合使用

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

可以使用 Visual Studio Code 和 [Azure Dev Spaces 扩展][azds-extension]，通过 Azure Dev Spaces 准备、运行和调试服务。 凭借 Visual Studio Code 和 Azure Dev Spaces 扩展，即可执行以下操作：

* 为在 AKS 中运行和调试服务生成资产
* 在开发环境中运行 Java、Node.js 和 .NET Core 服务
* 直接调试在开发环境中运行的 Java、Node.js 和 .NET Core 服务

## <a name="generate-assets"></a>生成资产

Visual Studio Code 和 Azure Dev Spaces 扩展可为你的项目生成以下资产：

* 用于使用 Maven 的 Java 应用程序、Node.js 应用程序和 .NET Core 应用程序的 Dockerfile
* 用于具有 Dockerfile 的几乎所有语言的 Helm 图表
* `azds.yaml` 文件，该文件是项目的 [Azure Dev Spaces 配置文件][azds-yaml]
* `.vscode` 文件夹，其中包含项目的 Visual Studio Code 启动配置（适用于使用 Maven 的 Java 应用程序、Node.js 应用程序和 .Net Core 应用程序）

Dockerfile、Helm 图表和 `azds.yaml` 文件是运行 `azds prep` 时生成的同类资产。 这些文件还可以在 Visual Studio code 之外适用于在 AKS 中运行项目，如运行 `azds up`。 `.vscode` 文件夹仅由 Visual Studio code 用于通过 Visual Studio Code 在 AKS 中运行项目。

## <a name="run-your-service-in-aks"></a>在 AKS 中运行服务

为项目生成资产后，便可以从 Visual Studio Code 在现有的开发环境中运行 Java、Node.js 和 .NET Core 服务。 在 Visual Studio Code 的“调试”页中，可以从 `.vscode` 目录调用启动配置来运行项目。

必须在 Visual Studio Code 外创建 AKS 群集并启用群集中的 Azure Dev Spaces。 可以重复使用在 Visual Studio Code 之外创建的现有 Dockerfile、Helm 图表和 `azds.yaml` 文件，例如通过运行 `azds prep` 生成的资产。 如果确实要重复使用在 Visual Studio Code 之外生成的资产，则仍需拥有 `.vscode` 目录。 该 `.vscode` 目录可由 Visual Studio Code 和 Azure Dev Spaces 扩展再生成，并且不会覆盖现有资产。

对于 .NET Core 项目，必须安装 [C# 扩展][csharp-extension]才能从 Visual Studio Code 运行 .NET 服务。 此外，对于使用 Maven 的 Java 项目，还必须安装[适用于 Azure Dev Spaces 扩展的 Java 调试程序][java-extension]，同时[安装并配置 Maven][maven] 才能从 Visual Studio Code 运行 Java 服务。

## <a name="debug-your-service-in-aks"></a>在 AKS 中调试服务

启动项目后，可以直接从 Visual Studio Code 调试在开发环境中运行的 Java、Node.js 和 .NET Core 服务。 `.vscode` 目录中的启动配置提供了其他调试信息，这些信息用于在开发环境中已启用调试的情况下运行服务。 Visual Studio Code 还会附加到开发环境中正在运行的容器中的调试过程，使你能够设置断点、检查变量并执行其他调试操作。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
