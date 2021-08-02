---
title: 跟踪严重性比下降 - Azure Application Insights
description: 使用 Azure Application Insights 监视应用程序跟踪，以便了解使用智能检测的跟踪遥测数据的异常模式。
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 776469f5e5cdab7796df1edccb680ab7151bbd18
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951521"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>跟踪严重性比下降（预览）

>[!NOTE]
>你可以将 Application Insights 资源迁移到警报库智能检测（预览版）。 迁移时会为各种智能检测模块创建警报规则。 创建这些规则后，就可以管理和配置它们，就像任何其他 Azure Monitor 警报规则一样。 你还可以为这些规则配置操作组，从而启用多种方法来针对新的检测执行操作或触发通知。
>
> 有关详细信息，请参阅[智能检测警报迁移](../alerts/alerts-smart-detections-migration.md)。
> 

跟踪广泛应用于应用程序，可以通过它们了解后台发生的事情。 出现错误时，跟踪可保证导致不理想状态事件序列的关键可见性。 虽然跟踪主要是非结构化的，但它们的严重性级别仍可以提供有价值的信息。 在应用程序稳定状态下，“良好”跟踪（Info 和 Verbose）和“错误”跟踪（Warning、Error 和 Critical）之间的比率应保持稳定      。 

由于各种原因（例如暂时性网络问题），出现一定程度的“错误”跟踪很正常。 但当一个真正的问题日益严重时，这通常表现为“错误”跟踪和“良好”跟踪之间的相关比率增大。 智能检测会自动分析应用程序记录的跟踪遥测数据，并且可以针对异常模式的严重性发出相关警告。

此功能除了需要为应用配置跟踪日志记录之外，不需要其他特殊设置。 了解如何为 [.NET](./asp-net-trace-logs.md) 或 [Java](./java-in-process-agent.md) 配置跟踪日志侦听器。 此功能在应用生成足够多的跟踪遥测数据时激活。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何时会收到此类型的智能检测通知？
对比前面七天计算的基线，如果“良好”跟踪（跟踪记录级别为 Info 或 Verbose）和“错误”跟踪（跟踪记录级别为 Warning、Error 或 Fatal）的比率在特定的某一天降低，则会收到此类通知    。

## <a name="does-my-app-definitely-have-a-problem"></a>收到通知是否意味着我的应用肯定有问题？
通知并不意味着应用肯定有问题。 虽然“良好”跟踪和“错误”跟踪的比率降低可能指示应用程序出现问题，但这种变化也可能是良性的。 例如，如果应用程序中的新流发出的“错误”跟踪多于现有流，则可能导致该比率提高。

## <a name="how-do-i-fix-it"></a>如何解决问题？
通知包括诊断信息，以在诊断进程中提供支持：
1. **会审。** 通知会显示有多少操作受到影响。 可以根据此信息为问题分配优先级。
2. **划分范围。** 该问题是影响所有流量，还是只影响某些操作？ 可以从通知中获取此信息。
3. **诊断。** 可以使用链接到支持信息的相关项和报告，帮助进一步诊断问题。