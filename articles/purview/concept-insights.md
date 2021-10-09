---
title: 了解 Azure Purview 中的见解报表
description: 本文介绍 Azure Purview 中的见解。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: ff50729dee893caa7a1c38f57cbd23f0470fc84b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218843"
---
# <a name="understand-insights-in-azure-purview"></a>了解 Azure Purview 中的见解

本文概述了 Azure Purview 中的见解功能。

见解是 Purview 的主要支柱之一。 此功能为客户提供了用于查看其目录的单个管理平台，并进一步旨在向数据源管理员、业务用户、数据专员、数据官和安全管理员提供特定的见解。 目前，Purview 的以下见解报告将在见解的公共预览期间提供给客户。

> [!IMPORTANT]
> Azure Purview 见解目前处于预览阶段。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="asset-insights"></a>资产见解

此报表提供了数据资产的鸟瞰视图，并将按源类型、分类和文件大小列出的分布作作为某些维度。 此报表适用于不同类型的用户，包括可能管理 Purview 帐户并运行扫描的用户，或可能对了解组织的数据资产中的特定分类有多少资产感兴趣的业务用户。 

此报表通过图形和 KPI，并深入探讨错误放置的文件等特定异常来提供广泛的见解。 此报表还支持端到端客户体验，客户可以在其中查看具有特定分类的资产计数，可以按源类型和顶级文件夹细分信息，还可以查看资产列表以进行进一步调查。

## <a name="scan-insights"></a>扫描见解

通过此报表，管理员可以了解扫描的总体运行状况 - 已成功的次数、已失败的次数、已取消的次数。 此报表提供了过去 7 天或过去 30 天内在 Purview 帐户中执行的扫描的状态更新。

此报表还允许管理员深入探讨并探索失败的扫描以及它们属于哪些特定的源类型。 为了进一步使用户能够调查，此报表可帮助他们导航到“源”体验中的“扫描历史记录”页。

## <a name="glossary-insights"></a>术语表见解

此报表为业务用户和数据专员提供了有关术语表的状态报表。 用户可以查看此报表来了解术语表术语按状态列出的分布，了解附加到资产的术语表术语数以及尚未附加到任何资产的术语表术语数。 业务用户还可以了解其术语表术语的完整性。 

此报表汇总了业务用户或数据专员需要关注的前几项，以便为其组织创建完整且可用的术语表。 用户还可以导航到“术语表见解”体验中的“术语表”体验，以便对特定术语表术语进行更改。

## <a name="classification-insights"></a>分类见解

此报表提供了有关已分类数据的位置、在扫描过程中找到的分类以及对已分类文件本身的明细信息。 它使安全管理员能够了解在其组织的数据资产中找到的信息类型。 

在 Azure Purview 中，分类类似于主题标签，用于标记和标识数据资产中的特定类型的内容。

使用分类见解报表来标识具有特定分类的内容并了解必需的操作，例如向存储库添加额外的安全性，或将内容移动到更安全的位置。

有关详细信息，请参阅[有关 Azure Purview 中的数据的分类见解](classification-insights.md)。

## <a name="sensitivity-labeling-insights"></a>敏感度标记见解

此报表提供了有关在扫描过程中找到的敏感度标签以及已标记文件本身的明细信息。 它使安全管理员能够确保在其组织的数据资产中找到的信息的安全性。 

在 Azure Purview 中，敏感度标签用于标识分类类型类别，以及要应用于每个类别的组安全策略。

使用标记见解报表来标识在内容中找到的敏感度标签并了解所需的操作，例如管理对特定存储库或文件的访问权限。

有关详细信息，请参阅[有关 Azure Purview 中的数据的敏感度标签见解](sensitivity-insights.md)。

## <a name="file-extension-insights"></a>文件扩展名见解

此报表提供了有关在扫描过程中找到的文件扩展名或文件类型的详细信息以及文件本身的明细信息。 

使用文件扩展名见解报表来了解每次拥有的文件数、这些文件所在的位置以及是否可以扫描其中的敏感数据。

有关详细信息，请参阅[有关 Azure Purview 中的数据的文件扩展名见解](file-extension-insights.md)。

## <a name="next-steps"></a>后续步骤

* [术语表见解](glossary-insights.md)
* [扫描见解](scan-insights.md)
* [分类见解](./classification-insights.md)
