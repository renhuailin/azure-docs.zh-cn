---
title: Teradata 中的元数据和世系
description: 本文介绍如何从 Teradata 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 39965089cdd838e12ac324737e977dbeee17b40b
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014939"
---
# <a name="how-to-get-lineage-from-teradata-into-azure-purview"></a>如何将从 Teradata 获取的世系传入 Azure Purview

本文将详细阐述 Azure Purview 中 Teradata 源的数据世系方面的内容。 若要查看 Purview 中 Teradata 的数据世系，必须先[扫描 Teradata](../purview/register-scan-teradata-source.md)。 

## <a name="lineage-of-teradata-artifacts-in-azure-purview"></a>Azure Purview 中 Teradata 项目的世系

用户可以按名称、说明或其他详细信息搜索 Teradata 项目，以查看相关结果。 在“资产概述与属性”选项卡下，将会显示说明、属性等基本详细信息。 在“世系”选项卡下，将会显示 Teradata 表与存储过程的资产关系，以及 Teradata 表与视图之间的资产关系。 

因此，我们支持存储过程中发生的数据转换的世系，以及 Teradata 表的视图中的转换的世系。 派生的世系也可在列级别使用。

:::image type="content" source="./media/how-to-lineage-teradata/lineage.png" alt-text="屏幕截图，显示如何为 Teradata 呈现世系。" lightbox="./media/how-to-lineage-teradata/lineage.png":::

在上面的屏幕截图中，customerView 是从 Teradata 表 test_customer 创建的 Teradata 视图。 此外，Return_DataSet 是一个使用 Teradata 表 test_customer 的存储过程。

## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- 如果要使用 ADF 将数据从 Teradata 移到 Azure，我们可以在数据移动运行时中跟踪世系 - [链接 Azure 数据工厂以推送自动化世系](how-to-link-azure-data-factory.md)
