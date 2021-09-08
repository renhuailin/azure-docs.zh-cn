---
title: 如何使用 Dynatrace Java OneAgent 监视 Azure Spring Cloud 应用程序
description: 如何使用 Dynatrace Java OneAgent 监视 Azure Spring Cloud 应用程序
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: cb4b82a37aef2f5e14be1445aa1fcb247ed37e15
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354385"
---
# <a name="how-to-monitor-azure-spring-cloud-applications-with-dynatrace-java-oneagent-preview"></a>如何使用 Dynatrace Java OneAgent（预览版）监视 Azure Spring Cloud 应用程序

本文介绍如何使用 Dynatrace Java OneAgent 监视 Azure Spring Cloud 应用程序。

使用 Dynatrace OneAgent，你可以：

* 使用 Dynatrace OneAgent 监视应用。
* 使用环境变量配置 Dynatrace OneAgent。
* 从 Dynatrace 仪表板检查所有监视数据。

以下视频介绍 Dynatrace OneAgent。

<br>

> [!VIDEO https://www.youtube.com/embed/PF0_SxuiZ2w]

## <a name="prerequisites"></a>先决条件

* [Azure CLI](/cli/azure/install-azure-cli)
* [Dynatrace 帐户](https://www.dynatrace.com/)
* [Dynatrace PaaS 令牌和租户令牌](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

## <a name="activate-dynatrace-oneagent"></a>Activate Dynatrace OneAgent

以下部分介绍如何激活 Dynatrace OneAgent。

### <a name="prepare-your-azure-spring-cloud-environment"></a>准备 Azure Spring Cloud 环境

1. 创建 Azure Spring Cloud 的实例。
1. 通过运行以下命令，创建一个要报告给 Dynatrace 的应用程序。 将占位符 \<...> 替换为自己的值。
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

### <a name="determine-the-values-for-the-required-environment-variables"></a>确定所需环境变量的值

要在 Azure Spring Cloud 实例上激活 Dynatrace OneAgent，需要配置四个环境变量：`DT_TENANT`、`DT_TENANTTOKEN`、`DT_CONNECTION_POINT` 和 `DT_CLUSTER_ID`。 有关详细信息，请参阅[将 OneAgent 与 Azure Spring Cloud 集成](https://www.dynatrace.com/support/help/shortlink/azure-spring)。

对于具有多个实例的应用程序，Dynatrace 可以通过多种方法将它们分组。 `DT_CLUSTER_ID` 是方法之一。 有关详细信息，请参阅[自定义进程组的结构](https://www.dynatrace.com/support/help/how-to-use-dynatrace/process-groups/configuration/adapt-the-composition-of-default-process-groups/)。

### <a name="add-the-environment-variables-to-your-application"></a>将环境变量添加到应用程序

可以使用 Azure 门户或 Azure CLI 将环境变量键/值对添加到应用程序。

#### <a name="option-1-azure-cli"></a>选项 1：Azure CLI

要使用 Azure CLI 添加键/值对，请运行以下命令，将占位符 \<...> 替换为前面步骤中确定的值。

```azurecli
az spring-cloud app deploy \
    --resource-group <your-resource-group-name> \
    --service <your-Azure-Spring-Cloud-name> \
    --name <your-application-name> \
    --jar-path app.jar \
    --env \
        DT_TENANT=<your-environment-ID> \
        DT_TENANTTOKEN=<your-tenant-token> \
        DT_CONNECTION_POINT=<your-communication-endpoint>
```

#### <a name="option-2-portal"></a>选项 2：门户

要使用 Azure 门户添加键/值对，请使用以下步骤：

1. 导航到现有应用程序的列表。

   :::image type="content" source="media/dynatrace-oneagent/existing-applications.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure Spring Cloud 应用部分。" lightbox="media/dynatrace-oneagent/existing-applications.png":::

1. 选择一个应用程序，导航到该应用程序的“概述”页面。

   :::image type="content" source="media/dynatrace-oneagent/overview-application.png" alt-text="应用程序“概述”部分的屏幕截图。" lightbox="media/dynatrace-oneagent/overview-application.png":::

1. 选择“配置”，以添加、更新或删除应用程序“环境变量”部分中的值 。

   :::image type="content" source="media/dynatrace-oneagent/configuration-application.png" alt-text="应用程序“配置”部分“环境变量”选项卡的屏幕截图。" lightbox="media/dynatrace-oneagent/configuration-application.png":::

## <a name="automation"></a>自动化

通过使用 Terraform 或 Azure 资源管理器模板（ARM 模板），你还可以运行预配自动化管道。 此管道可以提供完整的无干预体验，用于检测和监视你创建和部署的任何新应用程序。

### <a name="terraform"></a>Terraform

要在 Terraform 模板中配置环境变量，请将以下代码添加到模板中，并将 \<...> 占位符替换为自己的值。 有关详细信息，请参阅[管理活动的 Azure Spring Cloud 部署](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)。

```terraform
environment_variables = {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

### <a name="arm-template"></a>ARM 模板

要在 ARM 模板中配置环境变量，请将以下代码添加到模板中，并将 \<...> 占位符替换为自己的值。 有关详细信息，请参阅 [Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json)。

```ARM template
"environmentVariables": {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

## <a name="view-reports-in-dynatrace"></a>在 Dynatrace 中查看报告

本部分介绍如何在 Dynatrace 中查找各种报告。

> [!NOTE]
> Dynatrace 菜单和用户界面将逐渐演变。 为此，仪表板可能会移至 Dynatrace 网站的其他部分，而以下屏幕截图可能无法反映当前版本的用户界面。

在将环境变量添加到应用程序后，Dynatrace 开始收集数据。 要查看报告，请使用 [Dynatrace 菜单](https://www.dynatrace.com/support/help/get-started/navigation/)，转到“服务”，然后选择你的应用程序。

可以从“\<your-app-name>/详细信息/服务流”中找到“服务流” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png" alt-text="Dynatrace“服务流”报告的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png":::

可以从“\<your-app-name>/详细信息/方法热点”中找到“方法热点” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png" alt-text="“方法热点”报告的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png":::

可以从“\<your-app-name>/详细信息/响应时间分析”中找到“数据库语句” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png" alt-text="“响应时间分析”报告和“数据库语句”部分的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png":::

接下来，转到“多维分析”部分。

可以从“多维分析/常用数据库语句”中找到“常用数据库语句” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png" alt-text="“常用数据库语句”报告的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png":::

可以从“多维分析/异常概述”中找到“异常概述” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png" alt-text="“异常概述”报告的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png":::

接下来，转到“分析和优化”部分。

可以从“分析和优化/CPU 分析”中找到“CPU 分析” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png" alt-text="“CPU 分析”报告的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png":::

接下来，转到“数据库”部分。

可以从“数据库/详细信息/回溯”中找到“回溯” ：

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png" alt-text="“回溯”报告的屏幕截图。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png":::

## <a name="dynatrace-oneagent-logging"></a>Dynatrace OneAgent 日志记录

默认情况下，Azure Spring Cloud 会将 Dynatrace OneAgent 的“信息”级别日志打印到 `STDOUT`。 这些日志将与应用程序日志混合。 可以从应用程序日志中找到显式代理版本。

也可从以下位置获取 Dynatrace 代理的日志：

* Azure Spring Cloud 日志
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

可以应用 Dynatrace 提供的某些环境变量来配置 Dynatrace OneAgent 的日志记录。 例如，`DT_LOGLEVELCON` 控制日志的级别。

> [!CAUTION]
> 强烈建议不要替代由 Azure Spring Cloud 为 Dynatrace 提供的默认日志记录行为。 如果这样做，上述日志记录场景将被阻止，并且日志文件可能会丢失。 例如，不应将 `DT_LOGLEVELFILE` 环境变量输出到应用程序。

## <a name="dynatrace-oneagent-upgrade"></a>Dynatrace OneAgent 升级

Dynatrace OneAgent 自动升级已禁用，将每季度随 JDK 升级。 代理升级可能会影响以下场景：

* 升级前使用 Dynatrace OneAgent 的现有应用程序将保持不变，但需要重新启动或重新部署才能使用新版 Dynatrace OneAgent。
* 升级后创建的应用程序将使用新版 Dynatrace OneAgent。

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>VNet 注入实例出站流量配置

对于 Azure Spring Cloud 的 VNet 注入实例，需要确保为 Dynatrace OneAgent 正确配置了 Dynatrace 通信终结点的出站流量。 有关如何获取 `communicationEndpoints` 的信息，请参阅[部署 API - 获取 OneAgent 的连接信息](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/deployment/oneagent/get-connectivity-info/)。 有关详细信息，请参阅[在 VNET 中运行 Azure Spring Cloud 的客户责任](vnet-customer-responsibilities.md)。

## <a name="dynatrace-support-model"></a>Dynatrace 支持模型

有关在仅应用程序模式下部署 Dynatrace OneAgent 的限制信息，请参阅 [OneAgent 平台和功能支持矩阵](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix)的[云应用程序平台](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix/#cloud-application-platforms)部分。

## <a name="next-steps"></a>后续步骤

* [将分布式跟踪与 Azure Spring Cloud 配合使用](how-to-distributed-tracing.md)
