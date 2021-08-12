---
title: 快速入门 - 将 SDK 遥测数据导出到 Application Insights
titleSuffix: An Azure Communication Services quickstart
description: 了解如何将 Azure 通信服务 SDK 遥测数据导出到 Application Insights。
author: peiliu
manager: vravikumar
services: azure-communication-services
ms.author: peiliu
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: cddff62f911339e7b2ddb76535acda09e1793137
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292362"
---
# <a name="quickstart-using-azure-opentelemetry-exporter-to-export-sdk-telemetry-data-to-application-insights"></a>快速入门：使用 Azure OpenTelemetry 导出程序将 SDK 遥测数据导出到 Application Insights

Azure OpenTelemetry 导出程序是 [Azure Monitor](../../azure-monitor/index.yml) 中的 SDK。 通过此导出程序，可以使用 OpenTelemetry 导出跟踪数据并将数据发送到 [Application Insights](../../azure-monitor/app/app-insights-overview.md)。 OpenTelemetry 为应用程序和框架提供了一种标准化方法来收集遥测信息。

Azure Application Insights 是用于监视实时应用程序的 Azure Monitor 的一项功能。 这一功能可显示关于 Microsoft Azure 资源中应用程序的遥测数据。 标准化遥测模型可以创建不依赖于平台和语言的监测。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/telemetry-app-insights-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/telemetry-app-insights-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/telemetry-app-insights-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/telemetry-app-insights-python.md)]
::: zone-end

应用的输出描述每个已完成的操作：
<!---cSpell:disable --->
```console
Created an identity with ID: <identity-id>
Issued an access token with 'chat' scope that expires at <expiry-data>
```
<!---cSpell:enable --->

## <a name="view-the-telemetry-data-in-application-insights"></a>在 Application Insights 中查看遥测数据
若要从 SDK 分析遥测数据，请转到 `Performance` 选项卡，然后转到 `Dependencies`。 你将看到我们已经跟踪的 `Create User Activity` 和 `Get Token Activity`。

:::image type="content" source="media/application-insights-dependencies.png" alt-text="显示 Application Insights 中遥测数据条目的屏幕截图。":::

若要查看更多详细信息，可以深入了解这些示例：

:::image type="content" source="media/application-insights-samples-drill-down.png" alt-text="显示示例的向下钻取视图的屏幕截图":::

在向下钻取视图中，包含有关活动的详细信息，例如从何处调用活动、活动的时间戳、名称、性能、类型等。你还可以查看在上面的示例代码段中定义的云角色名称和实例 ID。 请注意，所跟踪的自定义属性也会显示在此处：

:::image type="content" source="media/application-insights-e2e-details.png" alt-text="事务详细信息的端到端视图":::

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 设置遥测导出程序
> * 将遥测数据汇集到 Application Insights
> * 查看 Application Insights 中的导出数据

你可能还想要：

- [了解有关在 Application Insights 中分析数据的详细信息](/powerapps/maker/canvas-apps/application-insights)