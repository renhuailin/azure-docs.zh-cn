---
title: 禁用异地备份
description: 如何在 Azure Synapse Analytics 中禁用专用 SQL DW)  (以前的 sql DW 的异地备份指南
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
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739085"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中禁用专用 SQL DW (以前的 SQL DW) 的异地备份

本文介绍如何禁用专用 SQL 池 (以前的 SQL DW) Azure 门户的异地备份。

## <a name="disable-geo-backups-through-azure-portal"></a>通过 Azure 门户禁用异地备份

按照以下步骤， (以前的 SQL DW) 来禁用专用 SQL 池的异地备份：

> [!NOTE]
> 如果禁用异地备份，你将无法再恢复 (以前的 SQL DW) 到其他 Azure 区域的专用 SQL 池。 
>

1. 登录到 [Azure 门户](https://portal.azure.com/)帐户。
1. 选择要为其禁用异地备份的专用 SQL 仓库 (以前的 SQL DW) 资源。 
1. 在左侧导航面板中的 " **设置** " 下，选择 " **异地备份策略**"。

   ![禁用异地备份](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. 若要禁用异地备份，请选择 " **禁用**"。 

   ![禁用异地备份](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. 选择 " *保存* " 以确保保存您的设置。 

   ![保存异地备份设置](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>后续步骤

- [还原现有专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-active-paused-dw.md)
- [还原已删除的专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-deleted-dw.md)
