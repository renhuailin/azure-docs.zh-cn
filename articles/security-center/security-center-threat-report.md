---
title: Azure 安全中心威胁智能报告 | Microsoft 文档
description: 本页可帮助你在调查过程中使用 Azure 安全中心威胁情报报告来查找有关安全警报的详细信息
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: b2a867c99e79b2ddfe9053589a1ebac9ab569490
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237014"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure 安全中心威胁情报报告

本页介绍 Azure 安全中心威胁情报报告如何帮助你详细了解有关触发了安全警报的威胁。


## <a name="what-is-a-threat-intelligence-report"></a>什么是威胁智能报告？

安全中心可以监视 Azure 资源、网络以及连接的合作伙伴解决方案中的安全信息，从而针对威胁进行保护。 分析该信息（通常需将多个来源的信息关联起来）即可确定威胁。 有关详细信息，请参阅 [Azure 安全中心如何检测和应对威胁](security-center-alerts-overview.md#detect-threats)。

当安全中心识别到威胁时，它将触发[安全警报](security-center-managing-and-responding-alerts.md)，其中包含有关事件的详细信息，包括修正建议。 为帮助事件响应团队调查威胁和采取补救措施，安全中心提供威胁情报报告，其中包含有关已检测到的威胁的信息。 该报告包含如下所示的信息：

* 攻击者的身份或关联项（如果此信息可用）
* 攻击者的目标
* 当前和历史攻击活动（如果此信息可用）
* 攻击者的策略、工具和过程
* 相关危害指标 (IoC)，例如 URL 和文件哈希
* 受害者研究，即研究行业和地理普遍性，帮助确定 Azure 资源有无风险
* 缓解计划和修复信息

> [!NOTE]
> 任何特定报表中的信息量都将有所不同；详细信息的级别基于恶意软件的活动和普遍性。

安全中心有三种类型的威胁报告，可因攻击而异。 可用报告有：

* **活动组报告**：深入分析攻击者、其目标和策略。
* **活动报告**：重点提供特定攻击活动的详细信息。
* **威胁摘要报告**：包含前两个报告中的所有项目。

在事件响应过程中，这种类型的信息很有用，该过程包括进行调查以了解攻击来源、攻击者动机以及可以执行什么操作来防止将来出现此问题。



## <a name="how-to-access-the-threat-intelligence-report"></a>如何访问威胁智能报告？

1. 在安全中心边栏中打开“安全警报”页面。
1. 选择警报。 
    此时将打开警报详细信息页面，其中包含有关警报的更多详细信息。 下面是“检测到勒索软件痕迹”警报详细信息页面。

    [![“检测到勒索软件痕迹”警报详细信息页面。](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. 选择指向报告的链接，随机将在默认浏览器中打开 PDF。

    [![“潜在的不安全操作”警报详细信息页面。](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    还可以选择性下载 PDF 报告。 

    >[!TIP]
    > 每个安全警报的可用信息量因警报类型而异。



## <a name="next-steps"></a>后续步骤

本页说明了如何在调查安全警报时打开威胁情报报告。 如需相关信息，请参阅以下页面：

* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理和响应安全警报。
* [处理 Azure 安全中心的安全事件](security-center-incident.md)