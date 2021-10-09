---
title: 自动完成到 Azure Spring Cloud 的应用程序部署
description: 介绍如何使用适用于 Azure Pipelines 的 Azure Spring Cloud 任务。
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: b3e5520f91301471bfc9df6bbd4a9cd4a8bc6a6a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666040"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>自动完成到 Azure Spring Cloud 的应用程序部署

本文演示如何使用[适用于 Azure Pipelines 的 Azure Spring Cloud 任务](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)来部署应用程序。

通过持续集成和持续交付工具，你可以快速地将更新部署到现有应用程序，工作量较少且风险较低。 Azure DevOps 可帮助你组织和控制这些关键作业。 

以下视频介绍如何使用你选择的工具（包括 Azure Pipelines）进行端到端自动化。

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

首先，创建 Azure 资源管理器服务到 Azure DevOps 项目的连接。 有关说明，请参阅[连接到 Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure)。 请务必选择用于 Azure Spring Cloud 服务实例的同一订阅。

## <a name="build-and-deploy-apps"></a>构建和部署应用

现在可以使用一系列任务来生成和部署项目。 以下 Azure Pipelines 模板定义变量、用于生成应用程序的 .NET Core 任务，以及用于部署应用程序的 Azure Spring Cloud 任务。

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>设置 Azure Spring Cloud 实例和 Azure DevOps 项目

首先，使用以下步骤设置要与 Azure DevOps 配合使用的现有 Azure Spring Cloud 实例。

1. 转到你的 Azure Spring Cloud 实例，然后创建一个新应用。 
1. 转到 Azure DevOps 门户，然后在选择的组织下创建一个新项目。 如果你没有 Azure DevOps 组织，可以免费创建一个。
1. 选择“存储库”，然后将 [Spring Boot 演示代码](https://github.com/spring-guides/gs-spring-boot)导入存储库。

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

接下来，创建 Azure 资源管理器服务到 Azure DevOps 项目的连接。 有关说明，请参阅[连接到 Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure)。 请务必选择用于 Azure Spring Cloud 服务实例的同一订阅。

## <a name="build-and-deploy-apps"></a>构建和部署应用

现在可以使用一系列任务来生成和部署项目。 以下部分介绍用于通过 Azure DevOps 部署应用的各种选项。

### <a name="deploy-using-a-pipeline"></a>使用管道进行部署

若要使用管道进行部署，请执行以下步骤：

1. 选择“管道”，然后使用 Maven 模板创建新管道。
1. 编辑 azure-pipelines.yml 文件，以将 `mavenPomFile` 字段设置为“complete/pom.xml” 。
1. 选择右侧的“显示助手”，然后选择“Azure Spring Cloud”模板 。
1. 选择你为 Azure 订阅创建的服务连接，然后选择你的 Spring Cloud 实例和应用实例。 
1. 禁用“使用过渡部署”。
1. 将“包或文件夹”设置为“complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar”。
1. 选择“添加”，将此任务添加到管道。
  
   你的管道设置应与下图匹配。

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="管道设置的屏幕截图。" lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   还可以使用以下管道模板生成和部署项目。 此示例首先定义用于生成应用程序的 Maven 任务，接下来定义第二个任务，该任务使用适用于 Azure Pipelines 的 Azure Spring Cloud 任务来部署 JAR 文件。

   ```yaml
   steps:
   - task: Maven@3
     inputs:
       mavenPomFile: 'complete/pom.xml'
   - task: AzureSpringCloud@0
     inputs:
       azureSubscription: '<your service connection name>'
       Action: 'Deploy'
       AzureSpringCloud: <your Azure Spring Cloud service>
       AppName: <app-name>
       UseStagingDeployment: false
       DeploymentName: 'default'
       Package: ./target/your-result-jar.jar
   ```

3. 选择“保存并运行”，然后等待作业完成。

### <a name="blue-green-deployments"></a>蓝绿部署

在上一部分介绍的部署在部署后会立即接收到应用程序流量。 这样，你便可以在应用程序接收任何客户流量之前，先在生产环境中对其进行测试。

#### <a name="edit-the-pipeline-file"></a>编辑管道文件

若要按照前面所述的相同方式生成应用程序并将其部署到过渡部署，请使用以下模板。 在此示例中，过渡部署必须已经存在。 有关替代方法，请参阅[蓝绿部署策略](concepts-blue-green-deployment-strategies.md)。

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

#### <a name="use-the-releases-section"></a>使用“发布”部分

以下步骤说明如何从“发布”部分启用蓝绿部署。

1. 选择“管道”，并为 Maven 生成和发布工件创建新管道。
   1. 选择“Azure Repos Git”作为代码位置。
   1. 选择代码所在的存储库。
   1. 选择“Maven”模板，并修改文件以将 `mavenPomFile` 字段设置为 `complete/pom.xml`。
   1. 选择右侧的“显示助手”，然后选择“发布生成工件”模板 。
   1. 将“发布路径”设置为“complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar”。
   1. 选择“保存并运行”。

1. 依次选择“发布”、“创建发布” 。
1. 添加新管道，然后选择“空作业”以创建作业。
1. 在“阶段”下，选择“1 个作业，0 个任务”行 

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="选择将任务添加到作业的界面的屏幕截图。":::

   1. 选择“+”以将任务添加到作业。
   1. 搜索“Azure Spring Cloud”模板，然后选择“添加”将任务添加到作业 。
   1. 选择“Azure Spring Cloud 部署:”以编辑任务。
   1. 使用应用信息填充此任务，然后禁用“使用过渡部署”。
   1. 启用“如果不存在过渡部署，则新建一个”，然后在“部署”中输入名称 。
   1. 选择“保存”以保存此任务。
   1. 选择“确定”。
1. 依次选择“管道”、“添加工件” 。
   1. 在“源(生成管道)”下，选择前面创建的管道。
   1. 依次选择“添加”和“保存”。
1. 在“阶段”下选择“1 个作业，1 个任务” 。
1. 导航到“阶段 1”中的“Azure Spring Cloud 部署”任务，然后选择“包或文件夹”旁边的省略号  。
1. 在对话框中选择“spring-boot-complete-0.0.1-SNAPSHOT.jar”，然后选择“确定”。

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="“选择文件或文件夹”对话框的屏幕截图。":::

1. 选择“+”，将另一个“Azure Spring Cloud”任务添加到作业 。
2. 将操作更改为“设置生产部署”。
3. 选择“保存”，然后选择“创建发布”以自动启动部署 。 

若要验证应用的当前发布状态，请选择“查看发布”。 完成此任务后，访问 Azure 门户以验证应用状态。

### <a name="deploy-from-source"></a>从源进行部署

若要直接部署到 Azure 而不执行单独的生成步骤，请使用以下管道模板。

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```

::: zone-end

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](./quickstart.md)
