---
title: Erwin 中的元数据和世系
description: 本文介绍如何从 Erwin 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: d1821ca94d8f5329ca4c08f65438a3d0ba6e4d48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862743"
---
# <a name="how-to-get-lineage-from-erwin-into-azure-purview"></a>如何将从 Erwin 获取的世系传入 Azure Purview

本文将详细阐述 Azure Purview 中 Erwin 源的数据世系方面的内容。 若要查看 Purview 中 Erwin 的数据世系，必须先[扫描 Erwin](../purview/register-scan-erwin-source.md)。 

## <a name="lineage-of-erwin-artifacts-in-azure-purview"></a>Azure Purview 中 Erwin 项目的世系

用户可以按名称、说明或其他详细信息搜索 Erwin 项目，以查看相关结果。 在“资产概述与属性”选项卡下，会显示说明、属性等基本详细信息。 “世系”选项卡下显示了 Erwin 表与视图之间的资产关系。因此，Erwin 视图会包含表中的世系信息。 

:::image type="content" source="./media/how-to-lineage-erwin/lineage.png" alt-text="屏幕截图，显示如何为 Erwin 呈现世系。" lightbox="./media/how-to-lineage-erwin/lineage.png":::


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
