---
title: 使用 Azure Pipelines 和 Bicep 文件实现 CI/CD
description: 介绍如何使用 Bicep 文件在 Azure Pipelines 中配置持续集成。 它演示如何使用 Azure CLI 任务来部署 Bicep 文件。
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/23/2021
ms.openlocfilehash: 28050d926bf5b4042ceb5b94796550bc517eb977
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289208"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>将 Bicep 与 Azure Pipelines 集成

你可以将 Bicep 文件与 Azure Pipelines 集成，以实现持续集成和持续部署 (CI/CD)。 本文介绍了如何使用 Azure CLI 管道任务来部署 Bicep 文件。

## <a name="prepare-your-project"></a>准备项目

本文假设 Bicep 文件和 Azure DevOps 组织已准备好创建管道。 以下步骤说明如何确保准备就绪：

* 你已有一个 Azure DevOps 组织。 如果没有，请[免费创建一个](/azure/devops/pipelines/get-started/pipelines-sign-up)。 如果你的团队已创建了一个 Azure DevOps 组织，请确保你是要使用的 Azure DevOps 项目的管理员。

* 已配置与 Azure 订阅之间的[服务连接](/azure/devops/pipelines/library/connect-to-azure)。 管道中的任务将以服务主体的身份执行。 有关创建连接的步骤，请参阅[创建 DevOps 项目](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)。

* 你有一个用于定义项目基础结构的 [Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。

## <a name="create-pipeline"></a>创建管道

1. 如果事先尚未添加管道，需要创建一个新管道。 在 Azure DevOps 组织中，选择“管道”和“新建管道”。 

   ![添加新管道](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 指定代码的存储位置。 在下图中，选择的是“Azure Repos Git”。

   ![选择代码源](./media/add-template-to-azure-pipelines/select-source.png)

1. 从该源中，选择包含项目代码的存储库。

   ![选择存储库](./media/add-template-to-azure-pipelines/select-repo.png)

1. 选择要创建的管道类型。 可以选择“初学者管道”。

   ![选择管道](./media/add-template-to-azure-pipelines/select-pipeline.png)

现已准备好添加 Azure PowerShell 任务，或者复制文件并部署任务。

## <a name="azure-cli-task"></a>Azure CLI 任务

以下 YAML 文件使用 [Azure CLI 任务](/azure/devops/pipelines/tasks/deploy/azure-cli)创建资源组并部署 Bicep 文件：

```yml
trigger:
- master

name: Deploy Bicep files

variables:
  vmImageName: 'ubuntu-latest'

  azureServiceConnection: '<your-connection-name>'
  resourceGroupName: '<your-resource-group-name>'
  location: '<your-resource-group-location>'
  templateFile: './azuredeploy.bicep'
pool:
  vmImage: $(vmImageName)

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: $(azureServiceConnection)
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az group create --name $(resourceGroupName) --location $(location)
      az deployment group create --resource-group $(resourceGroupName) --template-file $(templateFile)
```

Azure CLI 任务采用以下输入：

* `azureSubscription`，提供你创建的服务连接的名称。  请参阅[准备项目](#prepare-your-project)。
* `scriptType`，使用 bash。
* `scriptLocation`，使用 inlineScript 或 scriptPath。 如果指定 scriptPath，则还需指定 `scriptPath` 参数。
* `inlineScript`，指定你的脚本行。  示例中提供的脚本会生成一个名为 azuredeploy.bicep 的 bicep 文件，该脚本位于存储库的根目录中。

## <a name="next-steps"></a>后续步骤

* 要在管道中使用 What-if 操作，请参阅[在管道中使用 What-If 测试 ARM 模板](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)。
* 若要了解如何将 Bicep 文件与 GitHub Actions 结合使用，请参阅[使用 GitHub Actions 部署 Bicep 文件](./deploy-github-actions.md)。