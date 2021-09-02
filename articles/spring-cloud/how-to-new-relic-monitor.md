---
title: 如何使用 New Relic Java 代理进行监视
titleSuffix: Azure Spring Cloud
description: 了解如何使用 New Relic Java 代理监视 Azure Spring Cloud 应用。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-java
ms.openlocfilehash: 4f8773660846dfeef87c27ccbe0755a0ce37d325
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180434"
---
# <a name="how-to-monitor-with-new-relic-java-agent-preview"></a>如何使用 New Relic Java 代理（预览）进行监视

通过此功能，可使用 New Relic Java 代理监视 Azure Spring Cloud 应用。

通过 New Relic Java 代理，可以：
* 使用 New Relic Java 代理。
* 使用环境变量配置 New Relic Java 代理。
* 从 New Relic 仪表板查检查所有监视数据。

以下视频介绍如何使用 New Relic One 激活并监视在 Azure Spring Cloud 中运行的 Spring Boot 应用程序。

<br>

> [!VIDEO https://www.youtube.com/embed/4GQPwJSP3ys?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="prerequisites"></a>先决条件

* New Relic 帐户。
* [Azure CLI 2.0.67 或更高版本](/cli/azure/install-azure-cli)。

## <a name="leverage-the-new-relic-java-in-process-agent"></a>利用 New Relic Java 进程内代理

使用以下过程访问该代理：

1. 创建 Azure Spring Cloud 的实例。

2. 创建应用程序。

    ```azurecli
      az spring-cloud app create --name "appName" --is-public true \
      -s "resourceName" -g "resourceGroupName"
    ```

3. 使用 New Relic 代理和环境变量创建部署。

    ```azurecli
    az spring-cloud app deploy --name "appName" --jar-path app.jar \
       -s "resourceName" -g "resourceGroupName" \
       --jvm-options="-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar" \
       --env NEW_RELIC_APP_NAME=appName NEW_RELIC_LICENSE_KEY=newRelicLicenseKey
    ```

Azure Spring Cloud 会将 New Relic Java 代理预安装到 /opt/agents/newrelic/java/newrelic-agent.jar。 客户可从应用程序的“JVM 选项”中使用该代理，并使用 [New Relic Java 代理环境变量](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)配置该代理。

## <a name="portal"></a>门户

你还可通过以下过程从门户利用此代理。

1. 在导航窗格中的“设置”/“应用”中查找应用 。

   [![查找要监视的应用](media/new-relic-monitoring/find-app.png)](media/new-relic-monitoring/find-app.png)

2. 选择该应用程序以跳转到“概述”页。

   [![“概述”页](media/new-relic-monitoring/overview-page.png)](media/new-relic-monitoring/overview-page.png)

3. 选择左侧导航窗格中的“配置”，添加/更新/删除应用程序的环境变量。 

   [![更新环境变量](media/new-relic-monitoring/configurations-update-environment.png)](media/new-relic-monitoring/configurations-update-environment.png)

4. 选择“常规设置”，添加/更新/删除应用程序的 JVM 选项。 

   [![更新 JVM 选项](media/new-relic-monitoring/update-jvm-option.png)](media/new-relic-monitoring/update-jvm-option.png)

5. 从 New Relic 仪表板中查看应用程序 api/网关“摘要”页。

   [![应用“摘要”页](media/new-relic-monitoring/app-summary-page.png)](media/new-relic-monitoring/app-summary-page.png)

6. 从 New Relic 仪表板中查看应用程序 customers-service 的“摘要”页。
 
   [![Customers-service 页](media/new-relic-monitoring/customers-service.png)](media/new-relic-monitoring/customers-service.png)  

7. 从 New Relic 仪表板中查看“服务映射”页。  

   [![“服务映射”页](media/new-relic-monitoring/service-map.png)](media/new-relic-monitoring/service-map.png)

8. 从 New Relic 仪表板中查看应用程序的“JVM”页。

   [![JVM 页](media/new-relic-monitoring/jvm-page.png)](media/new-relic-monitoring/jvm-page.png)

9. 从 New Relic 仪表板中查看应用程序配置文件。

   [![应用程序配置文件](media/new-relic-monitoring/profile-app.png)](media/new-relic-monitoring/profile-app.png)

## <a name="new-relic-java-agent-logging"></a>New Relic Java 代理日志记录

默认情况下，Azure Spring Cloud 会将 New Relic Java 代理的日志输出到 `STDOUT`。 它将与应用程序日志相结合。 可从应用程序日志中获取显式代理版本。

还可从以下位置获取 New Relic 代理的日志：

* Azure Spring Cloud 日志。
* Azure Spring Cloud Application Insights。
* Azure Spring Cloud LogStream。

可以利用一些由 New Relic 提供的环境变量来配置新代理的日志记录（例如用于控制日志级别的 `NEW_RELIC_LOG_LEVEL`）。 有关详细信息，请参阅 [New Relic 环境变量](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables)。

> [!CAUTION]
> 强烈建议不要替代由 Azure Spring Cloud 为 New Relic 提供的日志记录默认行为。 如果这样做，上述场景中的日志记录场景将被阻止，日志文件可能会丢失。 例如，不应将以下环境变量传递给应用程序。 在重启或重新部署应用程序后，日志文件可能会丢失。
>
> * NEW_RELIC_LOG
> * NEW_RELIC_LOG_FILE_PATH

## <a name="new-relic-java-agent-updateupgrade"></a>New Relic Java 代理更新/升级

New Relic Java 代理将定期更新/升级 JDK。 代理更新/升级可能会影响以下场景。

* 更新/升级前使用 New Relic Java 代理的现有应用程序将保持不变。
* 更新/升级前使用 New Relic Java 代理的现有应用程序需要重启或重新部署，才能使用新版本的 New Relic Java 代理 。
* 更新/升级后创建的新应用程序将使用新版本的 New Relic Java 代理。

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Vnet 注入实例出站流量配置

对于 Azure Spring Cloud 的 vnet 注入实例，需要确保为 New Relic Java 代理正确配置出站流量。 有关详细信息，请参阅 [New Relic 的网络](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)。

## <a name="next-steps"></a>后续步骤

* [分布式跟踪和 Application Insights](how-to-distributed-tracing.md)
