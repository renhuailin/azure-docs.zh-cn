---
title: 如何使用 Azure Spring Cloud 中的 Application Insights Java 进程内代理
description: 如何使用 Azure Spring Cloud 中的 Application Insights Java 进程内代理监视应用和微服务。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c7083cb6669d7bc779a8e69babfef38988819f8c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483767"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Azure Spring Cloud 中的 Application Insights Java 进程内代理（预览版）

本文档介绍了如何使用 Azure Spring Cloud 中的 Application Insights Java 代理监视应用和微服务。 

使用此功能，你可以：

* 通过不同的筛选器搜索跟踪数据。
* 查看微服务的依赖项映射。
* 检查请求性能。
* 监视实时指标。
* 检查请求失败。
* 检查应用程序指标。

Application Insights 提供了许多可观察的角度，包括：

* 应用程序映射
* 性能
* 失败数
* 指标
* 实时指标
* 可用性

## <a name="enable-java-in-process-agent-for-application-insights"></a>启用 Application Insights 的 Java 进程内代理

通过以下过程启用 Java 进程内代理预览版功能。

1. 转到你的服务实例的服务概述页。
2. 单击“监视”边栏选项卡下的“Application Insights”条目。
3. 单击“启用 Application Insights”按钮以启用 Application Insights 集成。
4. 选择 Application Insights 的一个现有实例，或者创建一个新实例。
5. 单击“启用 Java 进程内代理”以启用预览版 Java 进程内代理功能。 你还可以在此处自定义从 0 到 100 的采样率。
6.  单击“保存”以保存更改。

## <a name="portal"></a>门户

1. 转到“服务 | 概述”页，然后在“监视”部分选择“Application Insights”  。 
2. 单击“启用 Application Insights”以在 Azure Spring Cloud 中启用 Application Insights。
3. 单击“启用 Java 进程内代理”以启用 Java IPA 预览版功能。 启用 IPA 预览版功能后，可以配置一个可选的采样率（默认值为 10.0%）。

  [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>使用 Application Insights 功能

启用 Application Insights 功能后，你可以：

在左侧导航窗格中，单击“Application Insights”以转到 Application Insights 的“概述”页面。 

* 单击“应用程序映射”，查看应用程序之间的调用的状态。

  [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 单击“客户-服务”和 `petclinic` 之间的链接可查看更多详细信息，例如 SQL 查询。

* 在左侧导航窗格中，单击“性能”可查看所有应用程序操作的性能数据，以及依赖项和角色。

  [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 在左侧导航窗格中，单击“失败”，查看应用程序中是否有任何异常。

  [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 在左侧导航窗格中，单击“指标”并选择命名空间，此时会看到 Spring Boot 指标和自定义指标（如果有）。

  [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 在左侧导航窗格中，单击“实时指标”可查看不同维度的实时指标。

  [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 在左侧导航窗格中，单击“可用性”可通过创建 [Application Insights 中的可用性测试](../azure-monitor/app/monitor-web-app-availability.md)来监视 Web 应用的可用性和响应性。

  [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM 模板
若要使用 Azure 资源管理器模板，请将以下内容复制到 `azuredeploy.json`。

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

## <a name="cli"></a>CLI
使用 CLI 命令应用 ARM 模板：

* 适用于现有的 Azure Spring Cloud 实例：

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* 适用于新建的 Azure Spring Cloud 实例：

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* 若要禁用 app-insight，请执行以下命令：

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>另请参阅
* [将分布式跟踪与 Azure Spring Cloud 配合使用](spring-cloud-howto-distributed-tracing.md)
* [分析日志和指标](diagnostic-services.md)
* [实时流式传输日志](spring-cloud-howto-log-streaming.md)
