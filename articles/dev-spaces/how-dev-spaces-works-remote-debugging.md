---
title: 使用 Azure Dev Spaces 远程调试代码的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 介绍用 Azure Dev Spaces 在 Azure Kubernetes 服务上进行远程调试的流程
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975036"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>使用 Azure Dev Spaces 远程调试代码的工作原理

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 在开发空间中运行项目后，Azure Dev Spaces 能提供一种在 AKS 中附加并调试运行中的应用程序的方法。

本文介绍使用 Dev Spaces 进行远程调试的工作原理。

## <a name="debug-your-code"></a>调试代码

对于 Java、.NET Core 和 Node.js 应用程序，可以使用 Visual Studio Code 或 Visual Studio 来调试在开发空间中直接运行的应用程序。 Visual Studio Code 和 Visual Studio 提供连接到开发空间、启动应用程序并附加调试程序的工具。 运行 `azds prep` 后，可以在 Visual Studio Code 或 Visual Studio 中打开项目。 Visual Studio Code 或 Visual Studio 将生成自己用于连接的配置文件，与运行 `azds prep` 独立开来。 在 Visual Studio Code 或 Visual Studio 中，可以设置断点并将应用程序启动至开发空间。

![调试代码](media/get-started-node/debug-configuration-nodejs2.png)

使用 Visual Studio Code 或 Visual Studio 启动应用程序以进行调试时，二者将以与运行 `azds up` 相同的方式进行启动并连接到开发环境。 此外，Visual Studio Code 和 Visual Studio 中的客户端工具均提供了一个附加参数，其中包含用于调试的特定信息。 该参数包含调试程序映像的名称、调试程序映像中调试程序的位置、以及应用程序的容器中装载调试程序文件夹的目标位置。

调试程序映像由客户端工具自动确定。 其使用的方法类似于运行 `azds prep` 时生成 Dockerfile 和 Helm 图表所用的方法。 调试程序装载到应用程序的映像后，便使用 `azds exec` 来运行。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](how-dev-spaces-works.md)
