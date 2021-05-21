---
title: 连接到 Azure Data Share
description: 本文介绍了如何连接 Azure Data Share 帐户与 Azure Purview 来搜索资产和跟踪数据世系。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552347"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>如何连接 Azure Data Share 与 Azure Purview

本文介绍了如何将 [Azure Data Share](../data-share/overview.md) 帐户与 Azure Purview 连接，并治理数据资产中的共享数据集（包括传出和传入数据集）。 各种数据治理角色可以跨组织、部门甚至数据中心等边界发现和跟踪数据世系。

## <a name="common-scenarios"></a>常见方案

Data Share 世系旨在为根本原因分析和影响分析提供详细信息。

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>方案 1：合作伙伴组织或内部部门传入/传出共享数据集的全方位视图

数据官可以查看与合作伙伴组织双向共享的所有数据集的列表。 他们可以按组织名称搜索和发现数据集，并查看所有传出和传入共享的完整视图。

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>方案 2：根本原因分析 - 对传入组织的数据集的上游依赖（传入共享的使用者视图）

由于来自外部 Data Share 帐户的上游数据问题，导致报表包含不正确的信息。 数据工程师可以了解上游故障和原因，并进一步联系共享所有者，以修复导致数据差异的问题。

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>方案 3：对组织传出数据集的影响分析（传出共享的提供者视图）

数据生产者想知道在对他们的数据集进行更改时谁会受到影响。 使用世系，数据生产者可以很容易地了解对通过 Azure Data Share 使用数据的下游内部或外部合作伙伴的影响。

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share 和 Purview 连接体验

若要连接 Azure Data Share 与 Azure Purview 帐户，请执行以下操作：

1. 创建 Purview 帐户。 所有 Data Share 世系信息将由 Purview 帐户收集。 可以使用现有 Purview 帐户，也可以新建一个。

1. 将 Azure Data Share 连接到 Purview 帐户。

    1. 在 Purview 门户中，可以转到“管理中心”，然后在“外部连接”部分下连接 Azure Data Share。
    1. 选择顶部栏中的“+ 新建”，在弹出侧栏中找到你的 Azure Data Share，并添加 Data Share 帐户。 在将 Data Share 连接到 Purview 帐户后，运行快照作业，以便在 Purview 中可以看到 Data Share 资产和世系信息。

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="用于链接 Azure Data Share 的管理中心":::

1. 在 Azure Data Share 中执行快照。

    - 在与 Azure Purview 建立 Azure Data Share 连接后，就可以对现有共享执行快照。 
    - 如果没有任何现有共享，请转到 Azure Data Share 门户来[共享数据](../data-share/share-your-data.md)[并订阅数据共享](../data-share/subscribe-to-data-share.md)。
    - 在共享快照完成后，就可以在 Purview 中查看关联的 Data Share 资产和世系。

1. 发现 Data Share 帐户，并在 Purview 帐户中共享信息。

    - 在 Purview 帐户的主页中，依次选择“按资产类型浏览”和“Azure Data Share”磁贴。 可以搜索帐户名、共享名称、共享快照或合作伙伴组织。 也可以在“搜索结果”页上应用帐户名、共享类型（已发送与已接收共享）筛选器。

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="“搜索结果”页中的 Azure Data Share":::

    >[!Important]
    >必须在将 Data Share 连接到 Purview 之后运行快照作业，才能让 Data Share 资产显示在 Purview 中。

1. 跟踪与 Azure Data Share 共享的数据集的世系。

    - 在 Purview“搜索结果”页中，选择 Data Share 快照（已接收/已发送），然后选择“世系”选项卡来查看包含上游和下游依赖关系的世系图。

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="使用 Azure Data Share 共享的数据集的世系":::

## <a name="next-steps"></a>后续步骤

- [目录世系用户指南](catalog-lineage-user-guide.md)
- [链接到 Azure 数据工厂以跟踪世系](how-to-link-azure-data-factory.md)
