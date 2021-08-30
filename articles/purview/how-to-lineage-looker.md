---
title: Looker 中的元数据和世系
description: 本文介绍如何从 Looker 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: bed8395940643e56d586369fd00341b4c28b27f9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862792"
---
# <a name="how-to-get-lineage-from-looker-into-azure-purview"></a>如何从 Looker 获取世系再转至 Azure Purview

本文将详细阐述 Azure Purview 中 Looker 源的数据世系相关方面。 若要查看 Purview 中 Looker 的数据世系，必须先[扫描 Looker。](../purview/register-scan-looker-source.md) 

## <a name="lineage-of-looker-artifacts-in-azure-purview"></a>Azure Purview 中 Looker 项目的世系

用户可以按名称、说明或其他详细信息搜索 Looker 项目，以查看相关结果。 在“资产概述与属性”选项卡下，将会显示说明、属性等基本详细信息。 “世系”选项卡下显示了 Looker 布局与视图之间的资产关系。因此，现在 Looker 视图将包含表中的世系信息。 

:::image type="content" source="./media/how-to-lineage-looker/lineage.png" alt-text="显示 Looker 如何呈现世系的屏幕截图。" lightbox="./media/how-to-lineage-looker/lineage.png":::


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
