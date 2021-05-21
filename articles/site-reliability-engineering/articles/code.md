---
title: 常见问题解答：SRE 和编码 |Microsoft Docs
titleSuffix: Azure
description: 常见问题解答：了解 SRE 与编码之间的关系
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089065"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>常见问题解答：开展 SRE 是否需要了解如何编码？

当个人考虑开展 SRE 以及团队考虑引入 SRE 做法时，提出的一个共同问题就是“是否需要了解如何编码？”

简单回答就是：需要。 

而详细的答案存在细微的差异。 我们将了解站点可靠性工程中用到编码的三个场景，以及每个场景所需的编码专业技能水平。 此列表并不全面，但这些场景是一些较常见的用例。

## <a name="scenario-1-removing-toil-through-automation"></a>场景 1：通过自动化消除“辛劳”

站点可靠性工程师和采用 SRE 做法的其他人员尝试尽可能地消除“辛劳”。 在 SRE 中，“辛劳”表示特定事项。 “辛劳”指的是由具有某些特征的人类完成的操作。 “辛劳”没有长期补偿价值。 它不会以任何有意义的方式推进服务。 它通常是重复操作并且主要是手动操作（即使可以自动化）。 当服务或系统随着时间的推移规模变大后，对该系统的请求数量也可能按比例增加，并且需要更多的手工劳动。

例如，如果服务要求 SRE 团队每周重置某些事项，要求手动预配新帐户和磁盘空间，或者反复手动重启它 - 这就是辛劳的操作负载。 完成这些操作并没有使服务以任何长期、持久的方式得到改善。 这些操作可能必须一遍又一遍地重复。

SRE 无法接受辛劳。 他们努力在可能且适当的时候消除辛劳。 这是自动化在 SRE 中发挥作用的地方之一。 如果这些请求可以自动处理，就可以让团队将精力投入到更有价值和影响力的事情。

编码专业技能：自动化需要使用一些编码专业技能，但不一定要求具备全面的软件工程技能。 如果你可以编写小脚本（可能是在 PowerShell 或 Bourne Shell 中），甚至通过极少量的代码创建 [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app)，此应用也能帮助你消除“辛劳”。

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>场景 2：通过 API/特定于域的语言 (DSL)/模板执行控制

虽然不是 SRE 工作所必需的，但如果能够通过 API、DSL 和模板控制环境（尤其是云环境），SRE 人员就可以扩展其工作范围。 通过编码，可以更高效地预配/取消预配基础结构、配置监视和集成多项服务。

编码专业技能：与前面的场景类似，这需要使用一些编码专业技能，但不一定要求具备全面的软件工程技能。 除了前面提及的脚本和逻辑应用之外，[https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) 还可以与最小编码体验一起使用。

## <a name="scenario-3-fixing-the-code"></a>方案 3：修复代码

站点可靠性工程师希望提高系统的可靠性。 实现此目标有时需要深入查看系统的源代码，从而确定问题，并且常常要向代码库返回修补程序。 这项工作的复杂程度视情况而异，但这些场景肯定会需要使用编码专业技能。

编码专业技能：此场景通常要求具备全面的软件工程专业技能。


## <a name="next-steps"></a>后续步骤

想要详细了解站点可靠性工程或“少代码工作”？ 请参阅上面链接的产品文档[站点可靠性工程中心](../index.yml)。
