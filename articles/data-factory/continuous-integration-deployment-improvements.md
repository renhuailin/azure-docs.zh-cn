---
title: 用于持续集成和交付的自动发布
description: 了解如何发布以自动进行持续集成和交付。
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 49ec43e59989f3fdad8f5731867953cc7cbb5757
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699702"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>用于持续集成和交付的自动发布

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概述

持续集成是指自动测试对基本代码所做的每项更改，并尽快进行持续集成，并将更改推送到过渡或生产系统中的测试。

在 Azure 数据工厂中，持续集成和交付 (CI/CD) 是指将数据工厂管道从一个环境（开发、测试、生产）移到另一个环境。 Azure 数据工厂利用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)存储各种 ADF 实体（管道、数据集、数据流等）的配置。 可通过两种建议的方式将数据工厂提升到另一个环境：

- 使用数据工厂与 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)集成的自动部署。
- 使用数据工厂 UX 与 Azure 资源管理器的集成手动上传资源管理器模板。

有关详细信息，请参阅 [Azure 数据工厂中的持续集成和交付](continuous-integration-deployment.md)。

本文重点介绍持续部署改进以及 CI/CD 的自动发布功能。

## <a name="continuous-deployment-improvements"></a>持续部署改进

"自动发布" 功能从 ADF UX 中获取 " *验证所有* 和导出 *AZURE 资源管理器 (ARM) 模板* 功能，并通过公开发布的 npm 包使逻辑可供使用 [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) 。 这允许你以编程方式触发这些操作，而无需转到 ADF UI 并单击按钮。 这会为 CI/CD 管道剖析持续集成体验。

### <a name="current-cicd-flow"></a>当前 CI/CD 流

1. 每个用户在其专用分支中进行更改。
2. 禁止推送到主节点，用户必须创建 PR 才能进行更改。
3. 用户必须加载 ADF UI，并单击 "发布" 将更改部署到数据工厂并在发布分支中生成 ARM 模板。
4. DevOps Release 管道配置为在每次将新更改推送到发布分支时，创建新发布和部署 ARM 模板。

![当前 CI/CD 流](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>手动步骤

在当前 CI/CD 流中，UX 是创建 ARM 模板的媒介，因此用户必须转到 ADF UI，并手动单击 "发布" 以启动 ARM 模板生成，并将其放在发布分支中，这是一种黑客的攻击。

### <a name="the-new-cicd-flow"></a>新的 CI/CD 流

1. 每个用户在其专用分支中进行更改。
2. 禁止推送到主节点，用户必须创建 PR 才能进行更改。
3. **每次向 master 进行新的提交时，都会触发 Azure DevOps 管道生成，验证资源并在验证成功时将 ARM 模板生成为项目。**
4. DevOps Release 管道配置为在每次有新的生成时都创建新的发布和部署 ARM 模板。 

![新 CI/CD 流](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>有何变化？

- 现在，我们有了一个使用 DevOps 生成管道的生成过程。
- 生成管道使用 ADFUtilities NPM 包，它将验证所有资源并)  (单模板和链接模板生成 ARM 模板。
- 生成管道负责验证 ADF 资源并生成 ARM 模板，而不是 ADF UI () 的 "发布" 按钮。
- DevOps 发布定义现在将使用这一新的生成管道，而不是 Git 项目。

> [!NOTE]
> 你可以继续使用现有机制 (adf_publish 分支) 或使用新流。 两者都受支持。 

## <a name="package-overview"></a>包概述

包中当前提供两个命令：
- 导出 ARM 模板
- 验证

### <a name="export-arm-template"></a>导出 ARM 模板

运行 npm 运行开始导出 <rootFolder> <factoryId> [outputFolder]，以使用给定文件夹的资源导出 ARM 模板。 此命令在生成 ARM 模板之前也将运行验证检查。 下面是一个示例：

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder 是表示数据工厂资源所在位置的必填字段。
- FactoryId 是以 "/subscriptions/ <subId> /ResourceGroups//providers/Microsoft.DataFactory/factories/" 格式表示数据工厂资源 ID 的必填字段 <rgName> <dfName> 。
- OutputFolder 是一个可选参数，它指定用于保存所生成 ARM 模板的相对路径。
 
> [!NOTE]
> 生成的 ARM 模板未发布到工厂的 `Live` 版本。 应使用 CI/CD 管道进行部署。 
 

### <a name="validate"></a>验证

运行 npm 运行开始验证 <rootFolder> <factoryId> 以验证给定文件夹的所有资源。 下面是一个示例：
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder 是表示数据工厂资源所在位置的必填字段。
- FactoryId 是以 "/subscriptions/ <subId> /ResourceGroups//providers/Microsoft.DataFactory/factories/" 格式表示数据工厂资源 ID 的必填字段 <rgName> <dfName> 。


## <a name="create-an-azure-pipeline"></a>创建 Azure 管道

尽管可以通过多种方式使用 npm 包，但其中一个主要优势是通过 [Azure 管道](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)使用的。 在每次合并到协作分支中时，可以触发一个管道，该管道首先验证所有代码，然后将 ARM 模板导出到可以由发布管道使用的 [生成项目](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) 。 **它与当前 CI/CD 过程的不同之处在于，您将在此项目中而不是在现有分支中指出您的发布管道 `adf_publish` 。**

请按照以下步骤开始操作：

1.  打开 Azure DevOps 项目并中转到 "管道"。 选择 "新建管道"。

    ![新建管道](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  选择要在其中保存管道 YAML 脚本的存储库。 建议将其保存在 ADF 资源的同一存储库中的 *生成* 文件夹中。 请确保存储库中的文件上还包含包名称 (，如以下示例中所示) **package.js** 。

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
3.  选择 " *Starter 管道*"。 如果已上传或合并 YAML 文件 (如下面的示例中所示) ，还可以直接指向该文件并对其进行编辑。 

    ![Starter 管道](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  在 YAML 代码中输入。 建议使用 YAML 文件并将其用作起点。
5.  保存并运行。 如果使用 YAML，则每次更新 "main" 分支时都会触发此方法。

## <a name="next-steps"></a>后续步骤

了解有关数据工厂中的持续集成和交付的详细信息： 

- [Azure 数据工厂中的持续集成和交付](continuous-integration-deployment.md)。
