---
title: 检测内存泄漏 - Azure Application Insights 智能检测
description: 使用 Azure Application Insights 监视应用程序是否存在可能的内存泄漏。
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: f2b13fc97baac2e8fcfb27475e6e018fdc0c5392
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536615"
---
# <a name="memory-leak-detection-preview"></a>内存泄漏检测（预览）

>[!NOTE]
>你可以将 Application Insights 资源迁移到警报库智能检测（预览版）。 迁移时会为各种智能检测模块创建警报规则。 创建后，可以像任何其他 Azure Monitor 警报规则一样管理和配置这些规则。 你还可以为这些规则配置操作组，从而启用多种方法来针对新的检测执行操作或触发通知。
>
> 有关详细信息，请参阅[智能检测警报迁移](../alerts/alerts-smart-detections-migration.md)。

智能检测自动分析应用程序中每个进程的内存消耗量，并可以就可能的内存泄漏或内存消耗量增加向你发出警告。

此功能需要[配置性能计数器](./performance-counters.md)，除此之外，不需要其他特殊设置。 当应用生成的内存性能计数器遥测数据（例如，专用字节数）足够多时，它处于活动状态。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何时会收到此类型的智能检测通知？
在属于你应用程序的一个或多个进程或计算机中，如果内存消耗量在较长的时间段内持续增加，将发出一个典型的通知。 机器学习算法用于检测与内存泄漏模式匹配的内存消耗增加。

## <a name="does-my-app-really-have-a-problem"></a>我的应用真的有问题吗？
通知并不意味着应用肯定有问题。 虽然内存泄漏模式很多时候指示应用程序有问题，但这些模式可能对特定进程而言是非常典型的，或者可能有自然的业务理由。 在这种情况下，可以忽略通知。

## <a name="how-do-i-fix-it"></a>如何解决问题？
通知包括诊断信息，以在诊断分析进程中提供支持：
1. **会审。** 通知显示增加的内存量（以 GB 为单位），以及内存增加的时间范围。 此信息可以帮助你对问题分配优先级。
2. **划分范围。** 多少台计算机表现出内存泄漏模式？ 可能内存泄漏期间触发了多少个异常？ 可以从通知中获取此信息。
3. **诊断。** 检测包含内存泄漏模式，该模式显示随时间推移进程的内存消耗量。 还可以使用链接到支持信息的相关项和报告，帮助进一步诊断问题。
