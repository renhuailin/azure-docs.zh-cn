---
title: 容器化用于 Service Fabric 网格的现有 .NET 应用
description: 向使用完整 .NET 框架的 ASP.NET 和控制台项目添加 Service Fabric 网格容器业务流程支持。
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625868"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>容器化用于 Service Fabric 网格的现有 .NET 应用

> [!IMPORTANT]
> Azure Service Fabric 网格的预览版已停用。 不允许再通过 Service Fabric 网格 API 进行新的部署。 对现有部署的支持将会持续到 2021 年 4 月 28 日。
> 
> 有关详细信息，请参阅 [Azure Service Fabric 网格预览版停用](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)。

本文介绍了如何向现有 .NET 应用添加 Service Fabric 网格容器业务流程支持。

在 Visual Studio 2017 中，可以向使用完整 .NET 框架的 ASP.NET 和控制台项目添加容器化支持。

> [!NOTE]
> 当前不支持 .NET **Core** 项目。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，可以在开始前[创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 请确保已[设置开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。 这包括安装 Service Fabric 运行时、SDK、Docker、Visual Studio 2017 以及创建本地群集。

## <a name="open-an-existing-net-app"></a>打开现有 .NET 应用

打开要向其中添加容器业务流程支持的应用。

如果要试用某个示例，可以使用 [eShop](https://github.com/MikkelHegn/ContainersSFLab) 代码示例。 本文的剩余部分假定我们使用该项目，但是你也可以将这些步骤应用于自己的项目。

获取 **eShop** 项目的副本：

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

下载后，在 Visual Studio 2017 中打开 **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**。

## <a name="add-container-support"></a>添加容器支持
 
使用 Service Fabric 网格工具向现有 ASP.NET 或控制台项目添加容器业务流程支持，如下所述：

在 Visual Studio 解决方案资源管理器中，右键单击项目名称（在示例中为 **eShopLegacyWebForms**），然后选择“添加” > “容器业务流程协调程序支持”。
此时将显示“添加容器业务流程协调程序支持”对话框。

![Visual Studio“添加容器业务流程协调程序”对话框](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

从下拉列表中选择“Service Fabric 网格”，然后单击“确定”。


>[!NOTE]
> 自 2020 年 11 月 2 日起，[下载速率限制](https://docs.docker.com/docker-hub/download-rate-limit/)将应用于 Docker 免费计划帐户对 Docker Hub 发出的匿名和经过身份验证的请求，并且由 IP 地址强制执行。 有关更多详细信息，请参阅[通过 Docker Hub 进行身份验证](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)。
>
> 为避免速率受限，务必将 Dockerfile 中的默认值 `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` 替换为 `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

然后，该工具将验证是否安装了 Docker，向你的项目中添加一个 Dockerfile，并为该项目拉取一个 Docker 映像。  
一个 Service Fabric 网格应用程序项目将添加到你的解决方案中。 它包含你的网格发布配置文件和配置文件。 该项目的名称与你的项目名称相同，并且将“Application”连接到末尾，例如 **eShopLegacyWebFormsApplication**。 

在新的网格项目中，你应当注意两个文件夹：
- **App Resources**，其中包含描述了其他网格资源（例如网络）的 YAML 文件。
- **Service Resources**，其中包含一个 service.yaml 文件，该文件描述了应用在部署后应当如何运行。

向你的应用添加容器业务流程支持后，可以按 **F5** 在本地 Service Fabric 网格群集上调试你的 .NET 应用。 下面是在 Service Fabric 网格群集上运行的 eShop ASP.NET 应用： 

![eShop 应用](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

现在可以将应用发布到 Azure Service Fabric 网格。

## <a name="next-steps"></a>后续步骤

查看如何将应用发布到 Service Fabric 网格：[教程 - 部署 Service Fabric 网格应用程序](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)