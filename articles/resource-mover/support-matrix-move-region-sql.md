---
title: 对利用 Azure 资源转移器在区域之间移动 Azure SQL 资源的支持。
description: 了解对利用 Azure 资源转移器在区域之间移动 Azure SQL 资源的支持。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452081"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>对在 Azure 区域之间移动 Azure SQL 资源的支持

本文总结了对使用 Azure 资源转移器在 Azure 区域之间移动 Azure SQL 资源的支持和先决条件。

## <a name="requirements"></a>要求

下表汇总了各项要求。

**功能** | **支持/不支持** | **详细信息**
--- | --- | ---
**Azure SQL 数据库超大规模** | 不支持 | 无法使用资源转移器移动 Azure SQL“超大规模”服务层级中的数据库。
**区域冗余** | 支持 |  支持的移动选项：<br/><br/> - 在支持区域冗余的区域之间移动。<br/><br/> - 在不支持区域冗余的区域之间移动。<br/><br/> - 从支持区域冗余的区域移动到不支持区域冗余的区域。<br/><br/> - 从不支持区域冗余的区域移动到支持区域冗余的区域。 
**数据同步** | 中心/同步数据库：不支持<br/><br/> 同步成员：。 | 如果移动了同步成员，你需要将 SQL 数据同步设置到新的目标数据库。
现有的异地复制 | 支持 | 现有的异地副本会重新映射到目标区域中的新主副本。<br/><br/> 在移动之后，必须初始化种子设定。 [了解更多信息](../azure-sql/database/active-geo-replication-configure-portal.md)
使用“创建自己的密钥”(BYOK) 进行透明数据加密 (TDE) | 支持 | [详细了解](../key-vault/general/move-region.md)如何在区域之间移动密钥保管库。
使用服务托管密钥进行 TDE | 。 |  [详细了解](../key-vault/general/move-region.md)如何在区域之间移动密钥保管库。
动态数据掩码规则 | 。 | 在移动过程中，规则会自动复制到目标区域。 [了解详细信息](../azure-sql/database/dynamic-data-masking-configure-portal.md)。
**高级数据安全性** | 不支持。 | 解决方法：在目标区域中进行 SQL Server 级别的设置。 [了解详细信息](../azure-sql/database/azure-defender-for-sql.md)。
**防火墙规则** | 不支持。 | 解决方法：在目标区域中为 SQL Server 设置防火墙规则。 数据库级防火墙规则会从源服务器复制到目标服务器。 [了解详细信息](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)。
审核策略 | 不支持。 | 在移动之后，策略将会重置为默认值。 [了解](../azure-sql/database/auditing-overview.md)如何重置。
**备份保留期** | 。 | 源数据库的备份保留策略将转移到目标数据库。 [了解](../azure-sql/database/long-term-backup-retention-configure.md)如何在移动之后修改设置。
自动优化 | 不支持。 | 解决方法：在移动之后设置自动优化设置。 [了解详细信息](../azure-sql/database/automatic-tuning-enable.md)。
数据库警报 | 不支持。 | 解决方法：在移动之后设置警报。 [了解详细信息](../azure-sql/database/alerts-insights-configure-portal.md)。
Azure SQL Server 延伸数据库 | 不支持 | 无法使用资源转移器移动 SQL Server 延伸数据库。
**Azure Synapse Analytics** | 不支持 | 无法使用资源转移器移动 Azure Synapse Analytics。
## <a name="next-steps"></a>后续步骤

尝试使用资源转移器将 [Azure SQL 资源](tutorial-move-region-sql.md)移动到另一区域。