---
title: Power BI 中的元数据和世系
description: 本文介绍如何从 Power BI 源提取数据世系。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b7143f7ccb0cb4c91cbb86cadb3cfa78c5c9bec3
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107912499"
---
# <a name="how-to-get-lineage-from-power-bi-into-azure-purview"></a>如何从 Power BI 获取世系再转至 Azure Purview

本文将详细阐述 Azure Purview 中 Power BI 源的数据世系相关方面。 若要查看 Purview 中 Power BI 的数据世系，必须先[扫描 Power BI。](../purview/register-scan-power-bi-tenant.md) 

## <a name="common-scenarios"></a>常见方案

1. 扫描 Power BI 源后，数据使用者可以从 Purview 对报表或仪表板执行根本原因分析。 对于报表中的任何数据差异，用户可以轻松识别上游数据集，并根据需要联系其所有者。

2. 数据生成者可以查看使用其数据集下游报表或仪表板。 在对数据集作出任何更改之前，数据所有者可以作出明智的决定。

2. 用户可以按名称、背书状态、敏感度标签、所有者、说明和其他业务方面进行搜索，以返回相关的 Power BI 项目。

## <a name="power-bi-artifacts-in-azure-purview"></a>Azure Purview 中的 Power BI 项目

[Power BI 扫描](../purview/register-scan-power-bi-tenant.md)完成后，以下 Power BI 项目将在 Purview 中详细列出

* 容量
* 工作区
* 数据流
* 数据集 
* 报表
* 仪表板

工作区项目将会显示数据流->数据集->报告->仪表板的世系

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-overview.png" alt-text="显示 Power BI 资产的“概述”选项卡如何呈现的屏幕截图。" lightbox="./media/how-to-lineage-powerbi/powerbi-overview.png":::

>[!Note]
> * PowerBI 数据集内当前不支持列世系和转换
> * 目前只显示用于创建 PowerBI 数据流或 PowerBI 数据集的数据源的有限信息。 例如：对于 PowerBI 数据集的 SQL server 源，只捕获服务器名称。 

## <a name="lineage-of-power-bi-artifacts-in-azure-purview"></a>Azure Purview 中 Power BI 项目的世系

用户可以按名称、说明或其他详细信息搜索 Power BI 项目，以查看相关结果。 在“资产概述与属性”选项卡下，描述、分类等基本详细信息和其他信息将会显示。 在“世系”选项卡下，显示的资产关系包含上游和下游依赖关系。

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-lineage.png" alt-text="显示 Power BI 如何呈现世系的屏幕截图。" lightbox="./media/how-to-lineage-powerbi/powerbi-lineage.png":::

## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
