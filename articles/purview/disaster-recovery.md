---
title: 适用于 Azure Purview 的灾难恢复
description: 了解如何为 Azure Purview 配置灾难恢复环境。
author: sudheerreddykoppula
ms.author: sukoppul
ms.service: purview
ms.topic: how-to
ms.date: 04/23/2021
ms.openlocfilehash: a5b06b204cf0124a73639a9c2c59a2bc05712cb7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214287"
---
# <a name="disaster-recovery-for-purview"></a>适用于 Purview 的灾难恢复

本文介绍如何为 Azure Purview 配置灾难恢复环境。 Azure 数据中心很少发生故障，但可能会持续数分钟到数小时之久。 数据中心发生故障可能会导致对数据管理所依赖的环境出现中断。 通过执行本文介绍的详细步骤，便可在 Purview 帐户的主要区域发生数据中心故障时继续管理数据。

## <a name="achieve-business-continuity-for-azure-purview"></a>实现 Azure Purview 的业务连续性

Azure Purview 实例中的业务连续性和灾难恢复 (BCDR) 是指能够让你的企业在面临中断时防止数据丢失并继续运营的机制、策略和过程，尤其是在其扫描、目录和见解层。 本页介绍如何为 Azure Purview 配置灾难恢复环境。

现如今，Azure Purview 不支持自动 BCDR。 在添加该支持前，你要负责处理备份和还原活动。 你可以在另一个区域手动创建 Purview 次级帐户作为热备用实例。

以下步骤说明了如何手动实现灾难恢复：

1. 在特定区域创建 Purview 主要帐户后，必须在 Azure 门户的不同区域中预配一个或多个 Purview 次级帐户。 

2. 在 Purview 主要帐户上执行的所有活动也必须在 Purview 次级帐户上执行。 这包括： 

    - 维护帐户信息
    - 创建和维护自定义扫描规则集、分类和分类规则
    - 注册和扫描源
    - 创建和维护集合以及源与集合之间的关联
    - 创建和维护扫描时使用的凭据。
    - 评判数据资产
    - 创建和维护术语表术语


制定手动 BCDR 计划时，请记住以下要点： 

- 你将要为 Purview 主要和次级帐户付费。 

- 不得将 Purview 主要和次级帐户配置为相同的 Azure 数据工厂、Azure Data Share 和 Synapse Analytics 帐户（如适用）。  因此，Azure 数据工厂和 Azure Data Share 的世系无法在 Purview 次级帐户中显示。 此外，与 Purview 主要帐户关联的 Synapse Analytics 工作区无法与 Purview 次级帐户关联。 此为当前限制，但会在支持自动 BCDR 时得到解决。 

- 集成运行时特定于 Purview 帐户。 因此，如果扫描必须在并行的 Purview 主要和次级帐户中运行，则必须维护多个自承载集成运行时。 当支持自动 BCDR 时，此限制也会得到解决。 

- 在同一源上对 Purview 主要和次级帐户并行执行扫描可能会影响到源的性能。 这可能导致不同 Purview 帐户的扫描持续时间有所差异。   

## <a name="related-information"></a>相关信息

- [业务连续性和灾难恢复](../best-practices-availability-paired-regions.md)
- [将高可用性纳入 BCDR 策略](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)
- [Azure 状态](https://status.azure.com/status)

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Purview，请参阅[创建 Azure Purview 帐户](create-catalog-portal.md)。
