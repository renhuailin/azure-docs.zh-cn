---
title: 异常卷的异常增加 - Azure Application Insights
description: 使用 Azure Application Insights 中的智能检测来监视应用程序异常，以便发现异常卷中的异常模式。
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4220415a0ab907c2cdf9268a59a4e85400cad86c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536695"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>异常卷的异常增加（预览）

>[!NOTE]
>你可以将 Application Insights 资源迁移到警报库智能检测（预览版）。 迁移时会为各种智能检测模块创建警报规则。 创建这些规则后，就可以管理和配置它们，就像任何其他 Azure Monitor 警报规则一样。 你还可以为这些规则配置操作组，从而启用多种方法来针对新的检测执行操作或触发通知。
>
> 有关详细信息，请参阅[智能检测警报迁移](../alerts/alerts-smart-detections-migration.md)。

智能检测会自动分析应用程序中引发的异常，并且可以针对异常遥测中的异常模式发出相关警告。

此功能需要为应用[配置异常报告](./asp-net-exceptions.md#set-up-exception-reporting)，除此之外，不需要其他特殊步骤。 此功能在应用生成足够多的异常遥测数据时激活。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何时会收到此类型的智能检测通知？
如果在一天中你的应用出现特定类型异常数异常增加的情况，你就会收到此类型的通知。 此数字会与针对过去七天计算的基线进行比较。
机器学习算法用于检测异常数的上升，同时考虑到了应用程序使用量的自然增长。

## <a name="does-my-app-definitely-have-a-problem"></a>收到通知是否意味着我的应用肯定有问题？
不是，通知并不意味着应用肯定有问题。 虽然异常数大量增加通常指示应用程序出现问题，但这些异常也可能是良性的并得到应用程序的正确处理。

## <a name="how-do-i-fix-it"></a>如何解决问题？
通知包括诊断信息，以在诊断进程中提供支持：
1. **会审。** 通知会显示有多少用户或多少请求受到影响。 可以根据此信息为问题分配优先级。
2. **划分范围。** 该问题是影响所有流量，还是只影响某些操作？ 可以从通知中获取此信息。
3. **诊断。** 检测包含的信息涉及从中引发异常的方法以及异常类型。 还可以使用链接到支持信息的相关项和报告，帮助进一步诊断问题。
