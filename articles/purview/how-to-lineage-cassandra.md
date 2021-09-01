---
title: 来自 Cassandra 的元数据和世系
description: 本文介绍如何从 Cassandra 源提取数据世系。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 68cee75d69be61623789db2c12ce83997646cd43
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014578"
---
# <a name="how-to-get-lineage-from-cassandra-into-azure-purview"></a>如何从 Cassandra 获取世系并传入 Azure Purview

本文将详细阐述 Azure Purview 中 Cassandra 源的数据世系方面的内容。 如要查看 Purview 中 Cassandra 的数据世系，必须先[扫描你的 Cassandra 服务器](../purview/register-scan-cassandra-source.md)。 

## <a name="lineage-of-cassandra-artifacts-in-azure-purview"></a>Azure Purview 中 Cassandra 项目的世系

用户可以按名称、说明或其他详细信息搜索 Cassandra 项目，以查看相关结果。 “资产概述与属性”选项卡下会显示说明、属性等基本详细信息。 “世系”选项卡下会显示 Cassandra 表与具体化视图之间的资产关系。因此，Cassandra 具体化视图现将包含表中的世系信息。 

派生的世系也可在列级别使用。

:::image type="content" source="./media/how-to-lineage-cassandra/lineage.png" alt-text="显示如何为 Cassandra 呈现世系的屏幕截图。" lightbox="./media/how-to-lineage-cassandra/lineage.png":::


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
