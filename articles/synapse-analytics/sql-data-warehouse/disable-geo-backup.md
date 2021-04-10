---
title: 禁用异地备份
description: 禁用 Azure Synapse Analytics 中专用 SQL 池（之前称为 SQL DW）的异地备份的操作指南
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739085"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>禁用 Azure Synapse Analytics 中专用 SQL 池（之前称为 SQL DW）的异地备份

本文介绍如何通过 Azure 门户禁用专用 SQL 池（之前称为 SQL DW）的异地备份。

## <a name="disable-geo-backups-through-azure-portal"></a>通过 Azure 门户禁用异地备份

按照以下步骤禁用专用 SQL 池（之前称为 SQL DW）的异地备份：

> [!NOTE]
> 如果禁用异地备份，你将无法再将专用 SQL 池（之前称为 SQL DW）恢复到其他 Azure 区域。 
>

1. 登录到 [Azure 门户](https://portal.azure.com/)帐户。
1. 选择要为其禁用异地备份的专用 SQL 池（之前称为 SQL DW）资源。 
1. 在左侧导航面板中的“设置”下，选择“异地备份策略” 。

   ![禁用异地备份](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. 若要禁用异地备份，请选择“禁用”。 

   ![已禁用异地备份](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. 选择“保存”以确保保存设置。 

   ![保存异地备份设置](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>后续步骤

- [还原现有专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-active-paused-dw.md)
- [还原已删除的专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-deleted-dw.md)
