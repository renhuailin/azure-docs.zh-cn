---
title: 自动完成到 Azure Spring Cloud 的应用程序部署
description: 介绍如何使用适用于 Azure Pipelines 的 Azure Spring Cloud 任务。
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2ac965e54a66eb2489ae7a4bfa9cac363f494b4f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182509"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>自动完成到 Azure Spring Cloud 的应用程序部署

本文演示如何使用[适用于 Azure Pipelines 的 Azure Spring Cloud 任务](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)来部署应用程序。

通过持续集成和持续交付工具，你可以快速地将更新部署到现有应用程序，工作量较少且风险较低。 Azure DevOps 可帮助你组织和控制这些关键作业。 

以下视频介绍如何使用你选择的工具（包括 Azure Pipelines）进行端到端自动化。

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

阅读[本文](/azure/devops/pipelines/library/connect-to-azure)，了解如何将 Azure 资源管理器服务连接创建到 Azure DevOps 项目。 请确保选择用于 Azure Spring Cloud 服务实例的同一订阅。

## <a name="build-and-deploy-apps"></a>构建和部署应用

::: zone pivot="programming-language-csharp"

### <a name="deploy-artifacts"></a>部署项目

可以使用一系列任务来生成和部署项目。 此代码片段定义变量、用于生成应用程序的 .NET Core 任务，以及用于部署应用程序的 Azure Spring Cloud 任务。

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

### <a name="deploy-artifacts"></a>部署项目

#### <a name="to-production"></a>部署到生产

可以使用一系列任务来生成和部署项目。 此代码片段首先定义用于生成应用程序的 Maven 任务，接下来定义第二个任务，该任务使用适用于 Azure Pipelines 的 Azure Spring Cloud 任务来部署 JAR 文件。

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
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: ./target/your-result-jar.jar
```

#### <a name="blue-green-deployments"></a>蓝绿部署

在上一部分介绍的部署在部署后会立即接收到应用程序流量。 有时，开发人员需要在生产环境中测试其应用程序，但测试又要在应用程序收到任何客户流量之前进行。

以下代码片段按上述方式生成应用程序，然后将它部署到过渡部署。 在此示例中，过渡部署必须已经存在。 有关替代方法，请参阅[蓝绿部署策略](concepts-blue-green-deployment-strategies.md)。

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

### <a name="deploy-from-source"></a>从源进行部署

无需单独的生成步骤便可直接部署到 Azure。

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
