---
title: Oracle 中的元数据和世系
description: 本文介绍如何从 Oracle 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: cf415250868c74aa95e22b12d8980987f0c9dc48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862767"
---
# <a name="how-to-get-lineage-from-oracle-into-azure-purview"></a>如何将从 Oracle 获取的世系传入 Azure Purview

本文将详细阐述 Azure Purview 中 Oracle 源的数据世系方面的内容。 若要查看 Purview 中 Oracle 的数据世系，必须先[扫描 Oracle](../purview/register-scan-oracle-source.md)。 

## <a name="lineage-of-oracle-artifacts-in-azure-purview"></a>Azure Purview 中 Oracle 项目的世系

用户可以按名称、说明或其他详细信息搜索 Oracle 项目，以查看相关结果。 在“资产概述与属性”选项卡下，描述、分类等基本详细信息和其他信息将会显示。 在“世系”选项卡下，会显示 Oracle 表与存储过程、视图和函数之间的资产关系。 

因此，当前的 Oracle 视图将包含来自表的世系信息，而函数和存储过程将使用 parameter_dataset 和 stored_procedure_result_set 在表/视图之间生成世系。 派生的世系也可在列级别使用。

:::image type="content" source="./media/how-to-lineage-oracle/lineage.png" alt-text="屏幕截图，显示如何为 Oracle 呈现世系。" lightbox="./media/how-to-lineage-oracle/lineage.png":::


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
