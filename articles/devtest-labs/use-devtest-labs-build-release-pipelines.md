---
title: 在 Azure Pipelines 生成和发布管道中使用开发测试实验室
description: 了解如何在 Azure Pipelines 生成和发布管道中使用 Azure 开发测试实验室。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: df9e496aa930c328832d59901aa8165e61cd076f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552616"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>在 Azure Pipelines 生成和发布管道中使用开发测试实验室
本文介绍如何在 Azure Pipelines 生成和发布管道中使用开发测试实验室。 

## <a name="overall-flow"></a>总体流
基本流是具有执行以下任务的生成管道：

1. 生成应用程序代码。
1. 在开发测试实验室中创建基础环境。
1. 用自定义信息更新环境。
1. 将应用程序部署到开发测试实验室环境
1. 测试代码。 

成功完成生成后，发布管道将使用生成工件部署过渡环境或生产环境。 

前提之一是，生成工件中必须提供重新创建已测试的生态系统所需的所有信息，包括 Azure 资源的配置。 由于使用 Azure 资源会产生成本，因此企业需要对这些资源的使用进行控制或跟踪。 在某些情况下，用于创建和配置资源的 Azure 资源管理器模板可能由其他部门（如 IT）管理。 而且，这些模板可能存储在不同的存储库中。 这会导致一个有趣的情况，即会创建并测试生成，并且需要在生成工件中同时存储代码和配置，才能在生产环境中正确地重新创建系统。 

通过在生成/测试阶段使用开发测试实验室，你可以将 Azure 资源管理器模板和支持文件添加到生成源，以便在发布阶段将用于测试的确切配置部署到生产环境中。 “创建 Azure 开发测试实验室环境”任务经过正确配置后将在生成工件中保存资源管理器模板。 在此示例中，你将使用[教程：在 Azure 应用服务中生成 .Net Core 和 SQL 数据库 Web 应用](../app-service/tutorial-dotnetcore-sqldb-app.md)中的代码，在 Azure 中部署并测试 Web 应用。

![总体流](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>设置 Azure 资源
需要预先创建以下项：

- 两个存储库。 第一个具有教程中的代码和一个带有两个附加 VM 的资源管理器模板。 第二个包含基本 Azure 资源管理器模板（现有配置）。
- 用于部署生产代码和配置的资源组。
- 需要通过与[配置库的连接](devtest-lab-create-environment-from-arm.md)来为生产管道建立实验室。 需要将资源管理器模板签入到配置存储库中（如 azuredeploy.json 与 metadata.json），以允许开发测试实验室识别并部署模板。

生成管道将创建开发测试实验室环境并部署代码进行测试。

## <a name="set-up-a-build-pipeline"></a>设置生成管道
在 Azure Pipelines 中，使用[教程：在 Azure 应用服务中生成 .Net Core 和 SQL 数据库 Web 应用](../app-service/tutorial-dotnetcore-sqldb-app.md)中的代码创建生成管道。 使用 ASP.NET Core 模板，该模板将填充生成、测试和发布代码所需的任务。

![选择 ASP.NET 模板](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

需要添加三个附加任务才能在开发测试实验室中创建环境并部署到环境中。

![包含三个任务的管道](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>创建环境任务
在创建环境任务（“创建 Azure 开发测试实验室环境”任务）中，使用下拉列表选择以下值：

- Azure 订阅
- 实验室名称
- 存储库名称
- 模板名称（显示存储环境的文件夹）。 

建议你使用页面上的下拉列表，而不是手动输入信息。 如果手动输入信息，请输入完全限定的 Azure 资源 ID。 任务显示易记名称，而不是资源 ID。 

环境名称是显示在开发测试实验室中的名称。 它应该是每个生成的唯一名称。 例如：TestEnv$(Build.BuildId)。 

你可以指定参数文件或参数以将信息传递到资源管理器模板。 

选择“基于环境模板输出创建输出变量”选项，然后输入引用名称。 在此示例中，请输入“BaseEnv”作为引用名称。 配置下一任务时将使用此 BaseEnv。 

![“创建 Azure 开发测试实验室环境”任务](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>填充环境任务
第二个任务（“填充 Azure 开发测试实验室环境”任务）是更新现有的开发测试实验室环境。 “创建环境”任务将输出 BaseEnv.environmentResourceId，此内容用于配置此任务的环境名称。 此示例的资源管理器模板具有两个参数：adminUserName 和 adminPassword 。 

![“填充 Azure 开发测试实验室环境”任务](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>应用服务部署任务
第三个任务是“部署 Azure 应用服务”任务。 应用类型设置为“Web 应用”，应用服务名称设置为“$ (网站)”。

![应用服务部署任务](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>设置发布管道
创建包含以下两个任务的发布管道：“Azure 部署：创建或更新资源组”和“部署 Azure 应用服务” 。 

对于第一个任务，请指定资源组的名称和位置。 模板位置是链接的项目。 如果资源管理器模板包含链接的模板，则需要实现自定义资源组部署。 模板位于已发布的删除项目中。 重写资源管理器模板的模板参数。 你可以将其余设置保留为默认值。 

对于第二个任务“部署 Azure 应用服务”，请指定 Azure 订阅，对于“应用类型”，请选择“Web 应用” ，对于“应用服务名称”，请选择“$ (网站)” 。 你可以将其余设置保留为默认值。 

## <a name="test-run"></a>测试运行
设置这两个管道后，手动将生成加入队列并查看其运行情况。 下一步是为生成设置适当的触发器，并将生成连接到发布管道。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [将 Azure 开发测试实验室集成到 Azure Pipelines 持续集成和交付管道](devtest-lab-integrate-ci-cd.md)
- [将环境集成到 Azure Pipelines CI/CD 管道中](integrate-environments-devops-pipeline.md)
