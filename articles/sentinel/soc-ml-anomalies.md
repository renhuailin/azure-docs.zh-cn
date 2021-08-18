---
title: 使用 SOC-ML 异常来检测 Azure Sentinel 中的威胁 | Microsoft Docs
description: 本文介绍如何在 Azure Sentinel 中使用新的 SOC-ML 异常检测功能。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: yelevin
ms.openlocfilehash: 51bc9923ce01116137b4ff447b94d08262b0c758
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734784"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-azure-sentinel"></a>使用 SOC-ML 异常来检测 Azure Sentinel 中的威胁

> [!IMPORTANT]
>
> - SOC-ML 异常目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="what-are-soc-ml-anomalies"></a>什么是 SOC-ML 异常？

随着攻击者和防御者不断在网络安全军备竞赛中争夺优势，攻击者一直想方设法逃避检测。 但是，攻击不可避免会导致系统受到攻击时出现异常行为。 Azure Sentinel 的 SOC-ML 基于机器学习的异常可以通过分析规则模板来识别此行为，这些模板可以开箱即用。 虽然单单异常本身不一定表明存在恶意行为，甚至是可疑行为，但可用于提高检测、调查和威胁搜寻：

- 用于提高检测的其他信号：安全分析师可以使用异常来检测新威胁并提高现有检测效率。 单个异常不是恶意行为的强信号，但与攻击链上不同点发生的多个异常结合时，其累积效果要强得多。 安全分析师还可以将异常所识别的异常行为作为触发警报的条件，从而增强现有检测。

- 调查期间的证据：安全分析师还可以在调查期间使用异常来帮助确认安全漏洞，找到调查漏洞的新途径，以及评估其潜在影响。 这些效率减少了安全分析师在调查上所花的时间。

- 主动威胁搜寻的开始：威胁搜寻者可以使用异常作为上下文来帮助确定其查询是否已发现可疑行为。 当行为可疑时，异常还指向进一步搜寻的潜在路径。 异常提供的这些线索会缩短检测威胁的时间，并降低其造成损害的机会。

异常可以是功能强大的工具，但众所周知其干扰非常大。 对于特定的环境或复杂的后处理，它们通常需要大量繁琐的优化。 Azure Sentinel SOC-ML 异常模板经由我们的数据科学团队的优化，可以提供开箱即用的值，但是，如果需要进一步优化这些值，过程很简单，且无需了解机器学习。 许多异常的阈值和参数可以通过已经很熟悉的分析规则用户界面进行配置和精细优化。 可以将原始阈值和参数的性能与界面中的新阈值和参数进行比较，并根据需要在测试或发布外部测试版阶段中进行进一步优化。 异常满足性能目标后，可通过单击按钮将具有新阈值或参数的异常提升到生产。 Azure Sentinel SOC-ML 异常既能够获得异常的优势，又省却了繁琐的工作。

## <a name="next-steps"></a>后续步骤

本文档介绍了 SOC-ML 如何帮助用户检测 Azure Sentinel 中的异常。

- 了解如何[查看、创建、管理和精细优化异常规则](work-with-anomaly-rules.md)。
- 了解[其他类型的分析规则](detect-threats-built-in.md)。
