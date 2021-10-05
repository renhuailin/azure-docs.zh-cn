---
title: 如何使用 Azure Spring Cloud 中的 Application Insights Java 进程内代理
description: 如何使用 Azure Spring Cloud 中的 Application Insights Java 进程内代理监视应用和微服务。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3922b716a5537838be06f3fec6a9626e59fa929f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129055092"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud"></a>Azure Spring Cloud 中的 Application Insights Java 进程内代理

本文介绍如何使用 Azure Spring Cloud 中的 Application Insights Java 代理监视应用和微服务。

使用此功能，你可以：

* 通过不同的筛选器搜索跟踪数据。
* 查看微服务的依赖关系映射。
* 检查请求性能。
* 监视实时指标。
* 检查请求失败。
* 检查应用程序指标。

Application Insights 提供许多可观察的视角，包括：

* 应用程序映射
* 性能
* 失败数
* 指标
* 实时指标
* 可用性

## <a name="enable-java-in-process-agent-for-application-insights"></a>启用 Application Insights 的 Java 进程内代理

使用以下过程启用 Java 进程内代理。

1. 转到你的服务实例的服务概述页。
2. 在“监视”窗格中选择“Application Insights”项 。
3. 选择“启用 Application Insights”以启用“Application Insights”集成 。
4. 选择 Application Insights 的一个现有实例，或者创建一个新实例。
   你还可以在此处自定义从 0 到 100 的采样率。
5. 选择“保存”以保存更改。

## <a name="portal"></a>门户

1. 转到“服务 | 概述”页，然后在“监视”部分选择“Application Insights”  。
2. 选择“启用 Application Insights”以在 Azure Spring Cloud 中启用 Application Insights。
3. 启用“Application Insights”后，可以配置一个可选采样率（默认值为 10.0%）。

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>使用 Application Insights 功能

启用 Application Insights 功能后，你可以：

在左侧导航窗格中，选择“Application Insights”以跳转到 Application Insights 的“概述”页 。

* 选择“应用程序映射”以查看在应用程序之间进行的调用的状态。

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 选择客户服务与 `petclinic` 之间的链接以查看更多详细信息，例如 SQL 查询。

* 在左侧导航窗格中，选择“性能”以查看所有应用程序的操作的性能数据以及依赖项和角色。

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 在左侧导航窗格中，选择“失败”以查看应用程序是否出现了意外情况。

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 在左侧导航窗格中，选择“指标”并选择命名空间，你将看到 Spring Boot 指标和自定义指标（如果有）。

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 在左侧导航窗格中，选择“实时指标”以查看不同维度的实时指标。

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 在左侧导航窗格中，选择“可用性”以通过创建 [Application Insights 中的可用性测试](../azure-monitor/app/monitor-web-app-availability.md)来监视 Web 应用的可用性和响应能力。

   [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="automation"></a>自动化

以下部分介绍如何使用 Azure 资源管理器模板（ARM 模板）或 Terraform 自动完成部署。
    
### <a name="arm-templates"></a>ARM 模板

若要使用 ARM 模板进行部署，请将以下内容复制到 azuredeploy.json 文件中。 有关详细信息，请参阅 [Microsoft.AppPlatform Spring/monitoringSettings](/azure/templates/microsoft.appplatform/spring/monitoringsettings)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

### <a name="terraform"></a>Terraform

对于 Terraform 部署，请使用以下模板。 有关详细信息，请参阅 [azurerm_spring_cloud_service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_service)。

```terraform
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_application_insights" "example" {
  name                = "tf-test-appinsights"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  application_type    = "web"
}

resource "azurerm_spring_cloud_service" "example" {
  name                = "example-springcloud"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku_name            = "S0"

  config_server_git_setting {
    uri          = "https://github.com/Azure-Samples/piggymetrics"
    label        = "config"
    search_paths = ["dir1", "dir2"]
  }

  trace {
    connection_string = azurerm_application_insights.example.connection_string
    sample_rate       = 10.0
  }

  tags = {
    Env = "staging"
  }
}
```

## <a name="cli"></a>CLI

可以使用 Azure CLI 命令管理 Application Insights。 在以下命令中，请务必将 \<placeholder> 文本替换为所述的值。 \<service-name> 占位符表示 Azure Spring Cloud 实例的名称。

若要在创建 Azure Spring Cloud 实例时配置 Application Insights，请使用以下命令。 对于 `app-insights` 参数，可以指定 Application Insights 名称或资源 ID。
   
```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name "serviceName" \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

还可以使用 Application Insights 连接字符串（首选）或检测密钥，如以下示例中所示。
   
```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

若要在创建 Azure Spring Cloud 实例时禁用 Application Insights，请使用以下命令：

```azurecli
az spring-cloud create \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable-app-insights
```

若要检查现有 Azure Spring Cloud 实例的 Application Insights 设置，请使用以下命令：

```azurecli
az spring-cloud app-insights show \
    --resource-group <resource-group-name> \
    --name <service-name>
```

若要使用连接字符串（首选）或检测密钥启用 Application Insights，请使用以下命令：

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights-key <connection-string-or-instrumentation-key> \
    --sampling-rate <sampling-rate>
```

若要使用资源名称或 ID 启用 Application Insights，请使用以下命令：

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --app-insights <name-or-resource-ID> \
    --sampling-rate <sampling-rate>
```

若要在现有 Azure Spring Cloud 实例上禁用 Application Insights，请使用以下命令：

```azurecli
az spring-cloud app-insights update \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --disable
```

## <a name="java-agent-updateupgrade"></a>Java 代理更新/升级

Java 代理将随 JDK 一起定期更新/升级，这可能会影响以下方案。

> [!Note]
> JDK 版本将每年每季度更新/升级一次。

* 更新/升级之前使用 Java 代理的现有应用程序不会受到影响。
* 更新/升级后创建的应用程序将利用新版本的 Java 代理。
* 以前未使用 Java 代理的现有应用程序需要重启或重新部署，然后才能利用新版 Java 代理。

## <a name="java-agent-configuration-hot-loading"></a>Java 代理配置热加载

Azure Spring Cloud 启用了热加载机制来调整代理配置的设置，而无需重启应用程序。

> [!Note]
> 热加载机制有几分钟延迟时间。

* 如果以前启用了 Java 代理，更改 Application Insights 实例和/或 SamplingRate 不需要重启应用程序。
* 如果启用 Java 代理，则必须重启应用程序。
* 禁用 Java 代理后，应用程序将在几分钟的延迟后停止发送所有监视数据。 你可以重启应用程序以从 Java 运行时环境中删除代理。

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Azure Spring Cloud 和 Application Insights 之间的概念匹配

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __应用程序映射__/角色<br />* __实时指标__/角色<br />* __故障__/角色/云角色<br />* __性能__/角色/云角色 |
| `App Instance`     | * __应用程序映射__/角色实例<br />* __实时指标__/服务名称<br />* __故障__/角色/云实例<br />* __性能__/角色/云实例 |

Azure Spring Cloud 中的名称 `App Instance` 将在以下情况下更改或生成：

* 创建新的应用程序。
* 将 JAR 文件或源代码部署到现有应用程序。
* 启动蓝/绿部署。
* 重启应用程序。
* 停止部署应用程序，然后重新启动应用程序。

如果数据存储在 Application Insights 中，它将包含自启用 Java 代理后创建或部署的 Azure Spring Cloud 应用实例的历史记录。 也就是说，在 Application Insights 门户中，可以看到昨天创建的应用程序数据，但这些数据会在特定时间范围内删除，例如最近的 24 小时。 以下场景展示了此过程：

* 今天上午 8:00 左右，你在启用 Java 代理的情况下在 Azure Spring Cloud 中创建了一个应用程序，然后在今天上午 8:10 左右将 JAR 文件部署至该应用程序。 在进行一些测试后，你更改了代码，并在今天上午 8:30 将新的 JAR 文件部署至该应用程序。 然后你休息了一会儿，并在上午 11:00 回来检查 Application Insights 中的部分数据。 你将看到：
  * 应用程序映射中的三个实例（时间范围为过去 24 小时）以及故障、性能和指标。
  * 应用程序映射中的一个实例（时间范围为过去 1 小时）以及故障、性能和指标。
  * 实时指标中的一个实例。
* 今天上午 8:00 左右，你在启用 Java 代理的情况下在 Azure Spring Cloud 中创建了一个应用程序，然后在今天上午 8:10 左右将 JAR 文件部署至该应用程序。 在今天上午 8:30 左右，你使用另一个 JAR 文件尝试了一次蓝/绿部署。 目前，此应用程序有两个部署。 休息到上午 11:00 后，你想要查看 Application Insights 中的某些数据。 你将看到：
  * 应用程序映射中的三个实例（时间范围为过去 24 小时）以及故障、性能和指标。
  * 应用程序映射中的两个实例（时间范围为过去 1 小时）以及故障、性能和指标。
  * 实时指标中的两个实例。

## <a name="see-also"></a>另请参阅

* [将分布式跟踪与 Azure Spring Cloud 配合使用](./how-to-distributed-tracing.md)
* [分析日志和指标](diagnostic-services.md)
* [实时流式传输日志](./how-to-log-streaming.md)
