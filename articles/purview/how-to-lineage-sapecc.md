---
title: SAP ECC 中的元数据和世系
description: 本文介绍如何从 SAP ECC 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 4c1e18f3f7484aec5bd99efbc4f9dea5b0e84ef8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014574"
---
# <a name="how-to-get-lineage-from-sap-ecc-into-azure-purview"></a>如何将从 SAP ECC 获取的世系传入 Azure Purview

本文将详细阐述 Azure Purview 中 SAP ECC 源的数据世系相关方面。 若要查看 Purview 中 SAP ECC 的数据世系，必须先[扫描 SAP ECC](../purview/register-scan-sapecc-source.md)。 

## <a name="lineage-of-sap-ecc-artifacts-in-azure-purview"></a>Azure Purview 中 SAP ECC 项目的世系

用户可以按名称、说明或其他详细信息搜索 SAP ECC 项目，以查看相关结果。 在“资产概述与属性”选项卡下，会显示说明、属性等基本详细信息。 “世系”选项卡下显示了 SAP ECC 表与视图之间的资产关系。因此，SAP ECC 视图会包含表中的世系信息。 

派生的世系也可在列级别使用。

:::image type="content" source="./media/how-to-lineage-sapecc/lineage.png" alt-text="屏幕截图，显示如何为 SAP ECC 呈现世系。" lightbox="./media/how-to-lineage-sapecc/lineage.png":::


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- 如果你要使用 ADF 将数据从 SAP ECC 移到 Azure，我们可以在数据移动运行时中跟踪世系 - [链接 Azure 数据工厂以推送自动化世系](how-to-link-azure-data-factory.md)
