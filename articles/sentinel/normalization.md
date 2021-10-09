---
title: 规范化和 Azure Sentinel 信息模型 (ASIM) | Microsoft Docs
description: 本文介绍 Azure Sentinel 如何使用 Azure Sentinel 信息模型 (ASIM) 规范化来自多种不同源的数据
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: e90171faf59daf70bde2150036fdf0a757e53979
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621388"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim-public-preview"></a>规范化和 Azure Sentinel 信息模型 (ASIM)（公共预览版）

Azure Sentinel 从多种源引入数据。 将各种数据类型和表一起处理时，需要了解每种数据类型和表，并为每种类型或架构编写并使用唯一的分析规则、工作簿和搜寻查询数据集。


有时，即使数据类型共享通用元素（例如防火墙设备），也需要单独的规则、工作簿和查询。 在调查和搜寻期间，在不同类型的数据之间进行关联也可能有难度。

本文概述了 Azure Sentinel 信息模型 (ASIM)，该模型提供了可应对多种数据类型的挑战的解决方案。

> [!TIP]
> 另请观看 [ASIM 网络研讨会](https://www.youtube.com/watch?v=WoGD-JeC7ng)或查看[网络研讨会幻灯片](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)。 有关详细信息，请参阅[后续步骤](#next-steps)。
>

> [!IMPORTANT]
> ASIM 当前处于预览状态。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="common-asim-usage"></a>常见 ASIM 使用情况

Azure Sentinel 信息模型 (ASIM) 通过提供以下功能，提供一种无缝的体验以用于在统一的规范化视图中处理各种源：

- 跨源检测。 规范化分析规则可跨源、在本地和云端工作，并检测攻击，如暴力攻击或不可能穿越系统（包括 Okta、AWS 和 Azure）。

- 源不可知内容。 使用 ASIM 的内置内容和自定义内容的覆盖范围会自动扩展到支持 ASIM 的任何源，即使源是在创建内容后添加的，也是如此。 例如，进程事件分析支持客户可能用于引入数据的任何源，如 Microsoft Defender for Endpoint、Windows 事件和 Sysmon。

- 内置分析中对自定义源的支持

- 易用性。 分析人员在了解 ASIM 后，编写查询就要简单得多，因为字段名称始终相同。

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM 和开放源代码安全事件元数据

Azure Sentinel 信息模型与[开放源代码安全事件元数据 (OSSEM)](https://ossemproject.com/intro.html) 通用信息模型相一致，可以在规范化表之间实现可预测的实体关联。

OSSEM 是社区导向型项目，主要注重于记录和标准化来自不同数据源与操作系统的安全事件日志。 该项目还提供一个通用信息模型 (CIM)，可供数据工程师在数据建模过程中使用，从而使安全分析师可以查询和分析不同数据源中的数据。

有关详细信息，请参阅 [OSSEM 参考文档](https://ossemproject.com/cdm/guidelines/entity_structure.html)。

## <a name="asim-components"></a>ASIM 组件

下图显示了如何将非规范化数据转换为规范化内容以及如何在 Azure Sentinel 中使用这些数据。 例如，可以从一个自定义的、特定于产品的非规范化表开始，使用分析器和规范化架构将该表转换为规范化数据。 将你的规范化数据用于 Microsoft 和自定义分析、规则、工作簿、查询等等。

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Azure Sentinel 中非规范化数据到规范化数据的转换流和用法":::

Azure Sentinel 信息模型包括以下组件：

|组件  |说明  |
|---------|---------|
|规范化架构     |   涵盖构建统一功能时可用的标准可预测事件类型集。 <br><br>每个架构定义了表示事件的字段、规范化列命名约定，以及字段值的标准格式。 <br><br> ASIM 当前定义了以下架构：<br> - [网络会话](normalization-schema.md)<br> - [DNS 活动](dns-normalization-schema.md)<br> - [进程事件](process-events-normalization-schema.md)<br> - [身份验证事件](authentication-normalization-schema.md)<br> - [注册表事件](registry-event-normalization-schema.md)<br> - [文件活动](file-event-normalization-schema.md)  <br><br>有关详细信息，请参阅 [Azure Sentinel 信息模型架构](normalization-about-schemas.md)。  |
|**分析器**     |  使用 [KQL 函数](/azure/data-explorer/kusto/query/functions/user-defined-functions)将现有数据映射到规范化架构。 <br><br>从 [Azure Sentinel GitHub 分析器文件夹](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers)部署 Microsoft 开发的规范化分析器。 规范化分析程序位于以 **ASim*** 开头的子文件夹中。  <br><br>有关详细信息，请参阅 [Azure Sentinel 信息模型分析程序](normalization-about-parsers.md)。     |
|每个规范化架构的内容     |    包括分析规则、工作簿、搜寻查询等。 每个规范化架构的内容适用于任何规范化数据，无需创建特定于源的内容。 <br><br>有关详细信息，请参阅 [Azure Sentinel 信息模型内容](normalization-content.md)。   |
| | |

### <a name="asim-terminology"></a>ASIM 术语

Azure Sentinel 信息模型使用以下术语：

|术语  |说明  |
|---------|---------|
|**报告设备**     |   向 Azure Sentinel 发送记录的系统。 此系统可能不是要为其发送记录的主题系统。      |
|**记录**     |从报告设备发送的数据单位。 记录通常称为 `log`、`event` 或 `alert`，但也可以是其他类型的数据。         |
|内容或内容项      |可以在 Azure Sentinel 中使用的不同的、可自定义的或用户创建的项目。 例如，这些项目包括分析规则、搜寻查询和工作簿。 内容项就是这样的一个项目。|
| | |

<br>

## <a name="getting-started-with-asim"></a>ASIM 入门指南

开始使用 ASIM：

1. 从 [Azure Sentinel GitHub 存储库](https://aka.ms/AzSentinelASim)快速部署所有 ASIM 分析程序。

1. 激活使用 ASIM 的分析规则模板。 有关详细信息，请参阅 [Azure Sentinel 信息模型 (ASIM) 内容列表](normalization-content.md#builtin)。

1. 使用以下方法在工作区中使用 ASIM：

    - 在 Azure Sentinel“日志”页中查询 KQL 中的日志时，使用 Azure Sentinel GitHub 存储库中的 ASIM 搜寻查询。 有关详细信息，请参阅 [Azure Sentinel 信息模型 (ASIM) 内容列表](normalization-content.md#builtin)。

    - 使用 ASIM 编写自己的分析规则，或[转换现有的分析规则](normalization-content.md#builtin)。

    - 通过为自定义源[编写分析程序](normalization-about-parsers.md)并将其[添加](normalization-about-parsers.md#include)到相关的源不可知分析程序，使自定义数据能够使用内置分析。

## <a name="next-steps"></a><a name="next-steps"></a>后续步骤

本文概述了 Azure Sentinel 和 Azure Sentinel 信息模型中的规范化。

有关详细信息，请参阅：

- 观看 [ASIM 网络研讨会](https://www.youtube.com/watch?v=WoGD-JeC7ng)或查看[幻灯片](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Azure Sentinel 信息模型架构](normalization-about-schemas.md)
- [Azure Sentinel 信息模型分析程序](normalization-about-parsers.md)
- [Azure Sentinel 信息模型内容](normalization-content.md)
