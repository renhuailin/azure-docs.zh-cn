---
title: BigQuery 中的元数据和世系
description: 本文介绍如何从 BigQuery 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 9373544ee3eab185438aafd7dbbdf661b385074e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862751"
---
# <a name="how-to-get-lineage-from-bigquery-into-azure-purview"></a>如何将从 BigQuery 获取的世系传入 Azure Purview

本文将详细阐述 Azure Purview 中 BigQuery 源的数据世系方面的内容。 若要查看 Purview 中 BigQuery 的数据世系，必须先[扫描 BigQuery 项目](../purview/register-scan-google-bigquery-source.md)。 

## <a name="lineage-of-bigquery-artifacts-in-azure-purview"></a>Azure Purview 中 BigQuery 项目的世系

用户可以按名称、说明或其他详细信息搜索 BigQuery 项目以查看相关结果。 在“资产概述与属性”选项卡下，会显示说明、属性等基本详细信息。 “世系”选项卡下显示了 BigQuery 表与视图之间的资产关系。因此，BigQuery 视图会包含表中的世系信息。 

派生的世系也可在列级别使用。

:::image type="content" source="./media/how-to-lineage-google-bigquery/lineage.png" alt-text="屏幕截图，显示如何为 BigQuery 呈现世系。" lightbox="./media/how-to-lineage-google-bigquery/lineage.png":::


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
