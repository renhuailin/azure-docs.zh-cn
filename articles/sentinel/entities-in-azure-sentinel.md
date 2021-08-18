---
title: 在 Azure Sentinel 中使用实体对数据进行分类和分析 | Microsoft Docs
description: 将实体分类（用户、主机名、IP 地址）分配给 Azure Sentinel 中的数据项，并使用这些分类来比较、分析和关联来自多个源的数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 60e92f28d36f9dd4e08338ac90b703f9536cf767
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726070"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>在 Azure Sentinel 中使用实体对数据进行分类和分析

## <a name="what-are-entities"></a>什么是实体？

当 Azure Sentinel 接收或生成警报时，这些警报会包含 Sentinel 可识别并分类为“实体”的数据项。 当 Azure Sentinel 了解特定数据项所代表的实体类型时，它就知道该就其提出哪些问题，然后它可在数据源的整个范围中比较有关该项的见解，还可轻松地对其进行跟踪，并在整个 Sentinel 体验（分析、调查、修正等搜寻等）中参考它。 实体的一些常见示例包括用户、主机、文件、进程、IP 地址和 URL。

### <a name="entity-identifiers"></a>实体标识符

Azure Sentinel 支持多种实体类型。 每种类型都有自己的唯一属性，包括一些可用于标识特定实体的属性。 这些属性在实体中表示为字段，被称为“标识符”。 请在下面查看受支持的实体及其标识符的完整列表。

#### <a name="strong-and-weak-identifiers"></a>强标识符和弱标识符

正如前文所述，对于每种类型的实体，都有可标识它的字段或字段集。 如果这些字段或字段集可唯一地标识一个实体而无歧义，则可称之为“强标识符”；如果它们在某些情况下可标识一个实体，但不保证在所有情况下都能唯一地标识一个实体，则可称之为“弱标识符” 。 但在很多情况下，可对所选的弱标识符进行组合来生成强标识符。

例如，可通过多种方式将用户帐户标识为“帐户”实体：使用单个强标识符，例如 Azure AD 帐户的数字标识符（GUID 字段）或其用户主体名称 (UPN) 值，或者使用弱标识符（例如其 Name 和 NTDomain 字段）的组合      。 不同的数据源可以不同的方式标识同一用户。 每当 Azure Sentinel 遇到两个实体，而这两个实体可根据其标识符被识别为同一实体时，它就会将这两个实体合并为一个实体，以便能够正确、一致地处理它们。

但是，如果你的某个资源提供程序创建了一个警报，其中显示没有充分识别实体（例如，仅使用一个弱标识符，比如没有域名上下文的用户名），那么不能将该用户实体与同一用户帐户的其他实例进行合并。 这些其他实例会被标识为一个单独的实体，而这两个实体将保持独立，不会合并。

为了尽量降低出现此情况的风险，应验证所有警报提供程序是否都正确地标识了其生成的警报中的实体。 此外，将用户帐户实体与 Azure Active Directory 进行同步可创建一个统一目录，从而能够合并用户帐户实体。

#### <a name="supported-entities"></a>受支持的实体

Azure Sentinel 中当前标识了以下类型的实体：

- 用户帐户
- 主机
- IP 地址
- 恶意软件
- 文件
- 进程
- 云应用程序
- 域名
- Azure 资源
- 文件哈希
- 注册表项
- 注册表值
- 安全组
- URL
- IoT 设备
- 邮箱
- 邮件群集
- 邮件消息
- 提交邮件

可在[实体参考](entities-reference.md)中查看这些实体的标识符及其他相关信息。

## <a name="entity-mapping"></a>实体映射

Azure Sentinel 在标识实体时如何识别警报中的一段数据？

让我们来看看如何在 Azure Sentinel 中处理数据。 数据是通过[连接器](connect-data-sources.md)从各种源中引入的，无论是服务到服务、基于代理，还是使用 syslog 服务和日志转发器都是如此。 数据存储在 Log Analytics 工作区的表中。 然后，系统会根据你定义并启用的分析规则按定期间隔查询这些表。 这些分析规则会执行很多操作，其中一项是将表中的数据字段映射到 Azure Sentinel 识别的实体。 根据你在分析规则中定义的映射，Azure Sentinel 将从查询返回的结果中获取字段，通过你为每种实体类型指定的标识符来识别这些字段，并对其应用这些标识符所标识的实体类型。

这样做的意义何在？

当 Azure Sentinel 能够标识来自不同类型的数据源的警报中的实体时，尤其是如果它可以使用每个数据源或第三方架构通用的强标识符来进行识别，它就可以在所有这些警报和数据源之间轻松进行关联。 这些关联有助于在实体上构建丰富的信息和见解，从而为你的安全操作奠定坚实的基础。

了解如何[将数据字段映射到实体](map-data-fields-to-entities.md)。

了解[可强标识实体的标识符](entities-reference.md)。

## <a name="entity-pages"></a>实体页

如果在搜索、警报或调查中遇到任何实体（目前仅限用户和主机），可选择该实体，随后将转到实体页，也就是包含该实体的所有有用信息的数据表。 在此页面上，可看到的信息类型包括实体基本信息、与此实体相关的重要事件的时间线，以及有关实体行为的见解。

实体页由 3 个部分组成：

- 左侧面板包含实体的标识信息，这些信息是从 Azure Active Directory、Azure Monitor、Azure 安全中心和 Microsoft Defender 等数据源中收集的。

- 中间面板显示与实体相关的重要事件（例如警报、书签和活动）的图形和文本时间线。 活动是 Log Analytics 中的重要事件的聚合。 检测这些活动的查询是由 Microsoft 安全研究团队开发的。

- 右侧面板显示关于该实体的行为见解。 这些见解可帮助快速识别异常和安全威胁。 见解是由 Microsoft 安全研究团队开发的，它们基于异常情况检测模型。

### <a name="the-timeline"></a>时间线

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="实体页时间线":::

时间线是实体页显示 Azure Sentinel 中的行为分析情况的主要部分。 它会显示与实体相关的事件，帮助你了解特定时间范围内的实体活动。

可在多个预设选项中选择时间范围（例如“过去 24 小时”），也可将其设置为任何自定义时间范围。 此外，你还可设置筛选器，将时间线中的信息限制为特定类型的事件或警报。

时间线中包含以下类型的项：

- 警报 - 将实体定义为“映射实体”的所有警报。 请注意，如果你的组织已创建[使用分析规则的自定义警报](./detect-threats-custom.md)，那么你应确保规则的实体映射已正确完成。

- 书签 - 包含页面上显示的特定实体的所有书签。

- 活动 - 与实体相关的重要事件的聚合。

### <a name="entity-insights"></a>实体见解

实体见解是 Microsoft 安全研究人员定义的查询，可帮助你的分析师更高效、更有效地进行调查。 见解显示在实体页的区域中，以表格数据和图表形式提供有关主机和用户的重要安全信息。 在此处显示信息意味着你无需访问 Log Analytics。 见解包含与登录、组添加、异常事件等内容相关的数据，还包含用于检测异常行为的高级 ML 算法。

见解基于以下数据源：

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Heartbeat（Azure Monitor 代理）
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>如何使用实体页

根据设计，实体页是多种使用方案的一部分，你可从事件管理、调查图和书签来访问它，也可从 Azure Sentinel 主菜单中“实体行为分析”下的实体搜索页来直接访问它。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="实体页用例":::

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Sentinel 中的实体。 如需实用的实施指南或要使用已获得的见解，请参阅以下文章：

- 在 Azure Sentinel 中[启用实体行为分析](./enable-entity-behavior-analytics.md)。
- [搜寻安全威胁](./hunting.md)。
