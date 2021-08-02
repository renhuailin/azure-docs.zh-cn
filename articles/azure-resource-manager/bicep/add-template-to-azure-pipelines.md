---
title: 使用 Azure Pipelines 和 Bicep 文件实现 CI/CD
description: 介绍如何使用 Bicep 文件在 Azure Pipelines 中配置持续集成。 它演示如何使用 PowerShell 脚本，或如何将文件复制到暂存位置并从该位置进行部署。
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 06/01/2021
ms.openlocfilehash: 663d7d74f292971c3421a6c853d7a0ee1325defe
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2021
ms.locfileid: "111528515"
---
# <a name="integrate-bicep-with-azure-pipelines"></a>将 Bicep 与 Azure Pipelines 集成

可将 Bicep 文件与 Azure Pipelines 集成，以实现持续集成和持续部署 (CI/CD)。 本文介绍了如何将 Bicep 文件生成到 Azure 资源管理器模板（ARM 模板），然后使用两种高级方法通过 Azure Pipelines 部署模板。

## <a name="select-your-option"></a>选择你的选项

在继续阅读本文之前，请考虑用于从管道部署 ARM 模板的不同选项。

* 使用 Azure CLI 任务。 在部署 ARM 模板之前，请使用此任务运行 `az bicep build` 以生成 Bicep 文件。

* 使用 ARM 模板部署任务。 此选项是最简单的选项。 如果要直接从存储库部署 ARM 模板，则可以使用此方法。 本文不介绍此选项，但 [ARM 模板与 Azure Pipelines 的持续集成](../templates/deployment-tutorial-pipeline.md)教程中会介绍此选项。 它展示了如何使用 [ARM 模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)从 GitHub 存储库部署模板。

* **添加用于运行 Azure PowerShell 脚本的任务**。 此选项的优势是可在整个开发生命周期中提供一致性，因为你可以使用运行本地测试时所用的同一脚本。 脚本将部署模板，但也可以执行其他操作，例如，获取要用作参数的值。 本文介绍了此选项。 请参阅 [Azure PowerShell 任务](#azure-powershell-task)。

   Visual Studio 提供包含 PowerShell 脚本的 [Azure 资源组项目](../templates/create-visual-studio-deployment-project.md)。 该脚本会将项目中的生成工件暂存到资源管理器可以访问的存储帐户。 生成工件是项目中的一些项，例如链接的模板、脚本和应用程序二进制文件。 如果要继续使用项目中的脚本，请使用本文中显示的 PowerShell 脚本任务。

* **添加用于复制和部署任务的任务**。 此选项可以方便地取代项目脚本。 在管道中配置两个任务。 一个任务将生成工件暂存到可访问的位置。 其他任务从该位置部署模板。 本文介绍了此选项。 请参阅[复制并部署任务](#copy-and-deploy-tasks)。

## <a name="prepare-your-project"></a>准备项目

本文假设 ARM 模板和 Azure DevOps 组织已准备好创建管道。 以下步骤说明如何确保准备就绪：

* 你已有一个 Azure DevOps 组织。 如果没有，请[免费创建一个](/azure/devops/pipelines/get-started/pipelines-sign-up)。 如果你的团队已创建了一个 Azure DevOps 组织，请确保你是要使用的 Azure DevOps 项目的管理员。

* 已配置与 Azure 订阅之间的[服务连接](/azure/devops/pipelines/library/connect-to-azure)。 管道中的任务将以服务主体的身份执行。 有关创建连接的步骤，请参阅[创建 DevOps 项目](../templates/deployment-tutorial-pipeline.md#create-a-devops-project)。

* 你有一个用于定义项目基础结构的 [ARM 模板](../templates/quickstart-create-templates-use-visual-studio-code.md)。

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

本部分演示如何在部署模板之前，将 Bicep 文件生成到 ARM 模板中。

以下 YAML 文件使用 [Azure CLI 任务](/azure/devops/pipelines/tasks/deploy/azure-cli) Bicep 文件：

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: 'script-connection'
    scriptType: bash
    scriptLocation: inlineScript
    inlineScript: |
      az --version
      az bicep build --file ./azuredeploy.bicep
```

对于 `azureSubscription`，请提供创建的服务连接的名称。

对于 `scriptType`，请使用 bash。

对于 `scriptLocation`，请使用 inlineScript 或 scriptPath 。 如果指定 scriptPath，则还需要指定 `scriptPath` 参数。

在 `inlineScript` 中，指定脚本行。  示例中提供的脚本生成名为 azuredeploy.bicep 的 bicep 文件，该文件位于存储库的根目录中。

## <a name="azure-powershell-task"></a>Azure PowerShell 任务

本部分介绍如何使用一个在项目中运行 PowerShell 脚本的任务来配置持续部署。 如果需要部署模板的 PowerShell 脚本，请参阅 [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) 或 [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1)。

以下 YAML 文件将创建 [Azure PowerShell 任务](/azure/devops/pipelines/tasks/deploy/azure-powershell)：

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

将任务设置为 `AzurePowerShell@5` 时，管道使用 [Az 模块](/powershell/azure/new-azureps-module-az)。

```yaml
steps:
- task: AzurePowerShell@3
```

对于 `azureSubscription`，请提供创建的服务连接的名称。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

对于 `scriptPath`，请提供管道文件中脚本的相对路径。 可以在存储库中查看该路径。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

在 `ScriptArguments` 中，提供脚本所需的任何参数。 以下示例演示脚本的一些参数，但你需要为脚本自定义参数。

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

选择“保存”时，生成管道将自动运行。 返回生成管道的摘要并观察状态。

![查看结果](./media/add-template-to-azure-pipelines/view-results.png)

可以选择当前正在运行的管道来查看有关任务的详细信息。 管道运行完成后，你将看到每个步骤的结果。

## <a name="copy-and-deploy-tasks"></a>复制并部署任务

本部分介绍如何使用两个任务来配置持续部署。 第一个任务将生成工件暂存到存储帐户，第二个任务部署模板。

若要将文件复制到存储帐户，必须为服务连接的服务主体分配存储 blob 数据参与者或存储 blob 数据所有者角色。 有关详细信息，请参阅 [AzCopy 入门](../../storage/common/storage-use-azcopy-v10.md)。

以下 YAML 显示了 [Azure 文件复制任务](/azure/devops/pipelines/tasks/deploy/azure-file-copy)。

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

需要根据环境修改此任务的多个部分。 `SourcePath` 指示生成工件相对于管道文件的位置。

```yaml
SourcePath: '<path-to-artifacts>'
```

对于 `azureSubscription`，请提供创建的服务连接的名称。

```yaml
azureSubscription: '<your-connection-name>'
```

对于存储和容器名称，请提供用于存储生成工件的存储帐户和容器的名称。 该存储帐户必须存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

创建复制文件任务后，可以添加该任务来部署暂存模板。

以下 YAML 显示了 [Azure 资源管理器模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)：

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

此任务有几个部分需要更详细地评审。

* `deploymentScope`：从以下选项中选择部署范围：`Management Group`、`Subscription` 和 `Resource Group`。

* `azureResourceManagerConnection`：提供创建的服务连接的名称。

* `subscriptionId`：提供目标订阅 ID。 此属性仅适用于资源组部署范围和订阅部署范围。

* `resourceGroupName` 和 `location`：提供要部署到的资源组的名称和位置。 如果该资源组不存在，任务将创建该资源组。

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

* `csmFileLink`：提供暂存模板的链接。 设置该值时，请使用从文件复制任务返回的变量。 以下示例链接到名为 mainTemplate.json 的模板。 包含名为 templates 的文件夹，因为文件复制任务将文件复制到该文件夹中。 在管道中，提供模板的路径和模板的名称。

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

管道如下所示：

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

选择“保存”时，生成管道将自动运行。 返回生成管道的摘要并观察状态。

## <a name="example"></a>示例

以下管道演示如何生成 Bicep 文件，以及如何部署已编译的模板：

:::code language="yml" source="~/resourcemanager-templates/bicep/azure-pipeline/deploy-bicep.yml":::

## <a name="next-steps"></a>后续步骤

* 要在管道中使用 What-if 操作，请参阅[在管道中使用 What-If 测试 ARM 模板](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)。
* 若要了解如何将 Bicep 文件与 GitHub Actions 结合使用，请参阅[使用 GitHub Actions 部署 Bicep 文件](./deploy-github-actions.md)。
