---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 06/13/2021
ms.author: memildin
ms.openlocfilehash: a490a08946a7357af41cce04051ef01765c8fbe5
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112062237"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

| 计划的更改                                                                                                                                                        | 预计更改日期 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | 2021 年 6 月                 |
| [SQL 数据分类建议的增强](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021 年第 3 季度                   |
| [启用 Azure Defender 安全控件以在安全分数中增加得分](#enable-azure-defender-security-control-to-be-included-in-secure-score)                       | 2021 年第 3 季度                   |
|                                                                                                                                                                       |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013

将从安全中心的监管合规性仪表板中删除 ISO 27001 的旧版实现。 如果要跟踪 ISO 27001 与安全中心的合规性，请针对所有相关管理组或订阅加入新的 ISO 27001:2013 标准，很快将会从仪表板中删除当前的旧版 ISO 27001。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="安全中心的监管合规性仪表板，其中显示了有关删除 ISO 27001 的旧版实现的消息。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL 数据分类建议的增强

**预计更改日期：** 2021 年第 3 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前 ID 为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>启用 Azure Defender 安全控件以在安全分数中增加得分

**预计更改日期：** 2021 年第 3 季度

安全中心的强化建议已分组到安全控件中。 每个控件都是相关安全建议的逻辑组，反映易受攻击的攻击面。 建议页面以及[安全控件及其简易](secure-score-security-controls.md#security-controls-and-their-recommendations)中的控件列表中清楚地显示了每个安全控件对总体安全分数的贡献。

自从引入“启用 Azure Defender”控件之后，该控件的最大可能分数一直是 0 分。 **通过这次更改，该控件将在安全分数中占一席之地。**

启用 Azure Defender 时，会将安全中心的免费模式的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 Azure Defender 的一些主要功能包括：为服务器集成了 Microsoft Defender for Endpoint 许可证、对虚拟机和容器注册表进行漏洞扫描、基于高级行为分析和机器学习的安全警报以及容器安全功能。 有关完整列表，请参阅 [Azure 安全中心免费版与已启用 Azure Defender 的版本的比较](security-center-pricing.md)。

进行此更改后，将影响不受 Azure Defender 保护的任何订阅的安全得分。 建议在进行此更改之前启用 Azure Defender，以确保不会对得分产生任何影响。 

在[快速入门：启用 Azure Defender](enable-azure-defender.md) 中了解详细信息。


## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。