---
title: 自动发布持续集成和交付
description: 了解如何自动发布持续集成和交付。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 08/23/2021
ms.openlocfilehash: 38e1d89a6934c603fa15c4b50e2309c57dc28622
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771806"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>自动发布持续集成和交付

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概述

持续集成是这样一种做法：自动测试对代码库所做的每项更改。 持续交付尽早执行在持续集成期间发生的测试并将更改推送到暂存系统或生产系统。

在 Azure 数据工厂中，持续集成和持续交付 (CI/CD) 是指将数据工厂管道从一个环境（如开发、测试和生产）移到另一个环境。 数据工厂使用 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)存储各种数据工厂实体（如管道、数据集和数据流）的配置。

可通过两种建议的方式将数据工厂提升到另一个环境：

- 使用数据工厂与 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 的集成进行自动化部署。
- 使用数据工厂用户体验与 Azure 资源管理器的集成手动上传 ARM 模板。

有关详细信息，请参阅 [Azure 数据工厂中的持续集成和交付](continuous-integration-deployment.md)。

本文重点介绍持续部署改进以及 CI/CD 的自动发布功能。

## <a name="continuous-deployment-improvements"></a>持续部署改进

自动发布功能采用数据工厂用户体验中的“全部验证”和“导出 ARM 模板”功能，并通过公开可用的 npm 包使逻辑可供使用 [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities)。 出于此原因，可以通过编程方式触发这些操作，而无需转到数据工厂 UI 并手动选择按钮。 此功能为 CI/CD 管道提供了真正的持续集成体验。

### <a name="current-cicd-flow"></a>当前 CI/CD 流

1. 每个用户在其专用分支中进行更改。
1. 不允许推送到主节点。 用户必须创建拉取请求才能进行更改。
1. 用户必须加载数据工厂 UI，选择“发布”以将更改部署到数据工厂，然后在发布分支中生成 ARM 模板。
1. 每次将新更改推送到发布分支时，DevOps 发布管道就会配置为创建新版本并部署 ARM 模板。

![显示当前 CI/CD 流的关系图。](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>手动步骤

在当前 CI/CD 流中，用户体验是创建 ARM 模板的中介。 因此，用户必须转到数据工厂 UI，手动选择“发布”以启动 ARM 模板生成，然后将其放在发布分支中。

### <a name="the-new-cicd-flow"></a>新的 CI/CD 流

1. 每个用户在其专用分支中进行更改。
1. 不允许推送到主节点。 用户必须创建拉取请求才能进行更改。
1. 每次向主节点作出新提交时，都会触发 Azure DevOps 管道生成。 如果验证成功，则它将验证资源并将 ARM 模板生成为项目。
1. 每次有新的生成可用时，DevOps 发布管道就会配置为创建新版本并部署 ARM 模板。

![显示新的 CI/CD 流的关系图。](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>有何变化？

- 现在，我们有了一个使用 DevOps 生成管道的生成过程。
- 生成管道使用 ADFUtilities NPM 包，该包将验证所有资源并生成 ARM 模板。 这些模板可以是单一模板，也可以是链接模板。
- 生成管道负责验证数据工厂资源并生成 ARM 模板，而不是数据工厂 UI（“发布”按钮）。
- DevOps 发布定义现在将使用这一新的生成管道，而不是 Git 项目。

> [!NOTE]
> 可以继续使用现有机制（即 `adf_publish` 分支），也可以使用新流。 两者都受到支持。

## <a name="package-overview"></a>包概述

包目前支持以下两个命令：

- 导出 ARM 模板
- 验证

### <a name="export-arm-template"></a>导出 ARM 模板

运行 `npm run build export <rootFolder> <factoryId> [outputFolder]` 以使用给定文件夹的资源导出 ARM 模板。 此命令还在生成 ARM 模板之前运行验证检查。 下面是一个示例：

```dos
npm run build export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` 是表示数据工厂资源所在位置的必填字段。
- `FactoryId` 是表示采用 `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` 格式的数据工厂资源 ID 的必填字段。
- `OutputFolder` 是一个可选参数，它指定用于保存所生成 ARM 模板的相对路径。

> [!NOTE]
> 生成的 ARM 模板未发布到工厂的实时版本。 应使用 CI/CD 管道进行部署。

### <a name="validate"></a>验证

运行 `npm run build validate <rootFolder> <factoryId>` 以验证给定文件夹的所有资源。 下面是一个示例：

```dos
npm run build validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` 是表示数据工厂资源所在位置的必填字段。
- `FactoryId` 是表示采用 `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` 格式的数据工厂资源 ID 的必填字段。

## <a name="create-an-azure-pipeline"></a>创建 Azure 管道

尽管可以通过多种方式使用 npm 包，但其中一个主要优势是通过 [Azure 管道](/azure/devops/pipelines/get-started/)使用的。 每次合并到协作分支中时，都可以触发一个管道，该管道首先验证所有代码，然后将 ARM 模板导出到可由发布管道使用的[生成项目](/azure/devops/pipelines/artifacts/build-artifacts)。 它与当前 CI/CD 过程的不同之处在于，你将在此项目（而不是现有 `adf_publish` 分支）中指出发布管道。

执行以下步骤以便开始：

1. 打开 Azure DevOps 项目，然后转到“管道”。 选择“新建管道”。

   ![显示“新管道”按钮的屏幕截图。](media/continuous-integration-deployment-improvements/new-pipeline.png)

2. 选择要在其中保存管道 YAML 脚本的存储库。 建议将其保存在数据工厂资源的同一存储库中的生成文件夹中。 确保存储库中有包含包名称的 package.json 文件，如以下示例中所示：

   ```json
   {
       "scripts":{
           "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
       },
       "dependencies":{
           "@microsoft/azure-data-factory-utilities":"^0.1.5"
       }
   } 
   ```

3. 选择“初学者管道”。 如果已上传或合并 YAML 文件（如以下示例中所示），也可以直接指向该文件并对其进行编辑。

   ![显示“初学者管道”的屏幕截图。](media/continuous-integration-deployment-improvements/starter-pipeline.png)

   ```yaml
   # Sample YAML file to validate and export an ARM template into a build artifact
   # Requires a package.json file located in the target repository
   
   trigger:
   - main #collaboration branch
   
   pool:
     vmImage: 'ubuntu-latest'
   
   steps:
   
   # Installs Node and the npm packages saved in your package.json file in the build
   
   - task: NodeTool@0
     inputs:
       versionSpec: '10.x'
     displayName: 'Install Node.js'
   
   - task: Npm@1
     inputs:
       command: 'install'
       workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
       verbose: true
     displayName: 'Install npm package'
   
   # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
   # Enter the appropriate subscription and name for the source factory.
   
   - task: Npm@1
     inputs:
       command: 'custom'
       workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
       customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
     displayName: 'Validate'
   
   # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
   # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
   
   - task: Npm@1
     inputs:
       command: 'custom'
       workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
       customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
     displayName: 'Validate and Generate ARM template'
   
   # Publish the artifact to be used as a source for a release pipeline.
   
   - task: PublishPipelineArtifact@1
     inputs:
       targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
       artifact: 'ArmTemplates'
       publishLocation: 'pipeline'
   ```

4. 输入你的 YAML 代码。 建议使用 YAML 文件作为起点。

5. 保存并运行。 如果使用了 YAML，则在每次更新主分支时都会触发该文件。

## <a name="next-steps"></a>后续步骤

详细了解数据工厂中的持续集成和交付：[Azure 数据工厂中的持续集成和交付](continuous-integration-deployment.md)。
