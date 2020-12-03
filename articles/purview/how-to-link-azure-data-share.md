---
title: 连接到 Azure 数据共享
description: 本文介绍如何使用 Azure 监控范围连接 Azure 数据共享帐户以搜索资产和跟踪数据沿袭。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552347"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>如何连接 Azure 数据共享和 Azure 监控范围

本文介绍如何将 [Azure 数据共享](../data-share/overview.md) 帐户连接到 azure 监控范围，并控制共享数据集 (数据空间中的传出和传入) 。 各种数据管理人员可以跨边界（如组织、部门甚至数据中心）发现和跟踪数据沿袭。

## <a name="common-scenarios"></a>常见方案

数据共享沿袭旨在提供有关根本原因分析和影响分析的详细信息。

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>方案 1: 360 对合作伙伴组织或内部部门共享的数据集的视图

数据安全官可以查看与合作伙伴组织双向共享的所有数据集的列表。 他们可以按组织名称搜索和发现数据集，并查看所有传出和传入共享的完整视图。

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>方案2：根本原因分析-对传入共享的数据集的上游依赖关系 (使用者视图) 

报表包含错误的信息，因为外部数据共享帐户存在上游数据问题。 数据工程师可以理解上游故障、了解原因，并进一步联系共享所有者，以解决导致其数据差异的问题。

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>方案3：对传出共享 (提供者视图的外部数据集的影响分析) 

数据生成者需要知道在对数据集进行更改时将受到什么影响。 使用沿袭，数据生成者可以轻松理解使用 Azure 数据共享使用数据的下游内部或外部合作伙伴的影响。

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure 数据共享和监控范围连接体验

若要连接 Azure 数据共享和 Azure 监控范围帐户，请执行以下操作：

1. 创建监控范围帐户。 监控范围帐户将收集所有数据共享沿袭信息。 您可以使用现有帐户，也可以创建新的监控范围帐户。

1. 将 Azure 数据共享连接到监控范围帐户。

    1. 在监控范围门户中，可以在 "管理中心" 下中转到 " **管理中心** "，并在 " **外部连接** " 部分连接 Azure 数据共享。
    1. 在顶部栏中选择 " **+ 新建** "，在弹出侧栏中查找 Azure 数据共享并添加数据共享帐户。 将数据共享连接到监控范围帐户后运行快照作业，以使数据共享资产和沿袭信息在监控范围中可见。

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="用于链接 Azure 数据共享的管理中心":::

1. 在 Azure 数据共享中执行快照。

    - 使用 Azure 监控范围建立 Azure 数据共享连接后，可以对现有共享执行快照。 
    - 如果没有任何现有共享，请使用 Azure 数据共享门户来[共享数据](../data-share/share-your-data.md)[并订阅数据共享](../data-share/subscribe-to-data-share.md)。
    - 共享快照完成后，可以在监控范围中查看关联的数据共享资产和沿袭。

1. 发现数据共享帐户并在你的监控范围帐户中共享信息。

    - 在监控范围帐户的主页中，选择 " **按资产类型浏览** "，并选择 " **Azure 数据共享** " 磁贴。 可以搜索帐户名称、共享名称、共享快照或合作伙伴组织。 否则，在 "搜索结果" 页上为帐户名称、共享类型 (发送与接收的共享) 应用筛选器。

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="&quot;搜索结果&quot; 页中的 Azure 数据共享":::

    >[!Important]
    >要使数据共享资产显示在监控范围中，必须在将数据共享连接到监控范围之后运行快照作业。

1. 跟踪与 Azure 数据共享共享的数据集的沿袭。

    - 从 "监控范围搜索结果" 页上，选择 (接收/发送的数据共享快照) 并选择 " **沿袭** " 选项卡，以查看具有上游和下游依赖项的沿袭图形。

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="使用 Azure 数据共享共享的数据集的沿袭":::

## <a name="next-steps"></a>后续步骤

- [目录沿袭用户指南](catalog-lineage-user-guide.md)
- [链接到 Azure 数据工厂进行沿袭](how-to-link-azure-data-factory.md)
