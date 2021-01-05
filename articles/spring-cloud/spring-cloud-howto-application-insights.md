---
title: 如何在 Azure 春季云中使用 Application Insights Java In-Process 代理
description: 如何使用 Azure 春季云中 Application Insights Java In-Process 代理监视应用和微服务。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: fb1c6f2b541e537bcb52c55fcfd87bbebe37b8e5
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831935"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>在 Azure 春季云中 Application Insights Java In-Process 代理 (预览) 

本文档介绍如何在 Azure 春季云中使用 Application Insights Java 代理监视应用和微服务。 

利用此功能，您可以：

* 通过不同的筛选器搜索跟踪数据。
* 查看微服务的依赖项映射。
* 检查请求性能。
* 监视实时实时指标。
* 检查请求失败。
* 检查应用程序指标。

Application Insights 提供了许多观察到的透视，包括：

* 应用程序映射
* 性能
* 失败数
* 指标
* 实时指标
* 可用性

## <a name="enable-java-in-process-agent-for-application-insights"></a>为 Application Insights 启用 Java In-Process 代理

使用以下过程启用 Java In-Process 代理预览功能。

1. 请参阅服务实例的 "服务概述" 页。
2. 单击 "监视" 边栏选项卡下的 **Application Insights** 项。
3. 单击 " **启用 Application Insights** " 按钮以启用 **Application Insights** 集成。
4. 选择 Application Insights 的现有实例，或者创建一个新实例。
5. 小鸡 **启用 java 进程内代理** 以启用预览版 java 进程内代理功能。 你还可以在此处自定义从0到100的采样速率。
6.  单击“保存”以保存更改。

## <a name="portal"></a>门户

1. 中转到 **服务 |"概述**" 页，然后在 "**监视**" 部分中选择 **Application Insights** 。 
2. 单击 " **启用 Application Insights** " 以在 Azure 春季云中启用 Application Insights。
3. 单击 " **启用 java 进程内代理** "，启用 java IPA 预览功能。 启用 IPA 预览功能后，可以 (默认的 10.0% ) 配置一个可选采样速率。

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>使用 Application Insights 功能

启用 **Application Insights** 功能后，可以：

在左侧导航窗格中，单击 " **Application Insights** " 以跳转到 Application Insights 的 " **概述** " 页。 

* 单击 " **应用程序映射** "，查看应用程序之间的调用的状态。

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 单击 "客户-服务" 之间的链接， `petclinic` 查看更多详细信息，如 SQL 查询。

* 在左侧导航窗格中，单击 " **性能** " 查看所有应用程序操作的性能数据，以及依赖项和角色。

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 在左侧导航窗格中，单击 " **故障** "，以查看应用程序是否出现意外情况。

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 在左侧导航窗格中，单击 " **指标** " 并选择 "命名空间"，你将看到 "弹簧启动指标" 和 "自定义指标" （如果有）。

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 在左侧导航窗格中，单击 " **实时指标** "，查看不同维度的实时度量值。

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 在左侧导航窗格中，单击 " **可用性** "，通过 [在 Application Insights 中创建可用性测试](/azure/azure-monitor/app/monitor-web-app-availability)来监视 Web 应用的可用性和响应能力。

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM 模板
若要使用 Azure 资源管理器模板，请将以下内容复制到中 `azuredeploy.json` 。

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

* 对于现有的 Azure 春季云实例：

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* 对于新创建的 Azure 春季云实例：

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* 若要禁用应用见解：

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>另请参阅
* [将分布式跟踪与 Azure Spring Cloud 配合使用](spring-cloud-tutorial-distributed-tracing.md)
* [分析日志和指标](diagnostic-services.md)
* [实时流式传输日志](spring-cloud-howto-log-streaming.md)