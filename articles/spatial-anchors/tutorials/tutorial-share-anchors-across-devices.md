---
title: 教程：在会话和设备间共享定位点
description: 在本教程中，你会了解如何使用后端服务在 Unity 中的 Android/iOS 设备之间共享 Azure 空间定位点标识符。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ed9b18144bb727af751fbcbbdd5cc6631afc40f
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "105727223"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>教程：在会话和设备间共享空间定位点

Azure 空间定位点是一种跨平台开发人员服务，通过该服务，你可以使用能够随时间推移跨设备保存自己位置的对象创建混合的现实体验。 

在本教程中，你将在一个会话期间使用 [Azure 空间定位点](../overview.md)来创建定位点，然后在相同或不同设备上查找它们。 也可由多个设备同时在相同位置查找相同的定位点。

![动画显示了几天内使用一个移动设备创建并在另一设备上使用的空间定位点。](./media/persistence.gif)


本教程介绍以下操作：

> [!div class="checklist"]
> * 在 Azure 部署可用于共享定位点的 ASP.NET Core Web 应用，并在指定的时间段内将定位点存储在内存中。
> * 配置来自快速入门的 Unity 示例中的 AzureSpatialAnchorsLocalSharedDemo 场景，以利用共享定位点 Web 应用。
> * 将定位点部署到一个或多个设备并运行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> 在本教程中会使用 Unity 和 ASP.NET Core Web 应用，不过此处的方法只是为了提供有关如何在其他设备间共享 Azure 空间定位点标识符的示例。 可以使用其他语言和后端技术实现相同目的。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>下载示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>部署共享定位点服务

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

打开 Visual Studio，然后打开 Sharing\SharingServiceSample 文件夹中的项目。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

在 Visual Studio Code 中部署服务之前，需要创建资源组和应用服务计划。

### <a name="sign-in-to-azure"></a>登录 Azure

转到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>，然后登录到 Azure 订阅。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

在“资源组”旁边，选择“新建”。

将资源组命名为 myResourceGroup，然后选择“确定” 。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

在“托管计划”旁边，选择“新建”。

在“配置托管计划”窗格中，使用以下设置：

| 设置 | 建议的值 | 描述 |
|-|-|-|
|应用服务计划| MySharingServicePlan | 应用服务计划的名称 |
| 位置 | 美国西部 | 托管 Web 应用的数据中心 |
| 大小 | 免费 | 确定托管功能的[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

选择“确定”  。

打开 Visual Studio Code，然后打开 Sharing\SharingServiceSample 文件夹中的项目。 

若要通过 Visual Studio Code 部署共享服务，请遵循<a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2&preserve-view=true#open-it-with-visual-studio-code" target="_blank">通过 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure</a> 中的说明。 从“通过 Visual Studio Code 打开它”部分开始。 请不要按照上面步骤中的说明创建另一个 ASP.NET 项目，因为你已有要部署和发布的项目：SharingServiceSample。

---

## <a name="deploy-the-sample-app"></a>部署示例应用

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你在 Azure 中部署了 ASP.NET Core Web 应用，还配置并部署了 Unity 应用。 你使用应用创建了空间定位点，然后使用 ASP.NET Core Web 应用与其他设备共享它们。

可以改进 ASP.NET Core Web 应用，使其使用 Azure Cosmos DB 来保存共享的空间定位点标识符的存储。 通过添加 Azure Cosmos DB 支持，你可以让 ASP.NET Core Web 应用立即创建定位点。 然后，通过使用存储在 Web 应用中的定位点标识符，你可以让应用在几天后返回以再次找到定位点。

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 存储定位点](./tutorial-use-cosmos-db-to-store-anchors.md)