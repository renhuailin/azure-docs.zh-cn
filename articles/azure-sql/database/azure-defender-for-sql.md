---
title: Azure Defender for SQL
description: 了解用于管理数据库漏洞以及检测可能对 Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse 中数据库造成威胁的异常活动的功能。
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452300"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender for SQL 是高级 SQL 安全功能的统一包。 Azure Defender 可用于 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics。 它包括用于发现和分类敏感数据、呈现和减少潜在数据库漏洞，以及检测可能表明数据库有威胁的异常活动的功能。 它提供用于启用和管理这些功能的一个转到位置。

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 有哪些优点？

Azure Defender 提供一组高级 SQL 安全功能，包括 SQL 漏洞评估和高级威胁防护。
- [漏洞评估](sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪并帮助修正潜在的数据库漏洞。 它可用于直观查看安全状态，包括解决安全问题的可操作步骤，并可加强数据库的防御工事。
- [高级威胁防护](threat-detection-overview.md)检测异常活动，指出尝试访问或利用数据库的行为异常且可能有害。 它连续监视数据库中的可疑活动，并针对潜在漏洞、Azure SQL 注入攻击和异常数据库访问模式提供即时安全警报。 高级威胁防护警报提供可疑活动的详细信息，并建议如何调查和缓解威胁。

启用 Azure Defender for SQL 之后，其包含的所有功能都会启用。 只需单击一次，即可为 Azure 或 SQL 托管实例中[服务器](logical-servers.md)上的所有数据库启用 Azure Defender。 需要属于 [SQL 安全管理器](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色或者数据库或服务器管理员角色才能启用或管理 Azure Defender 设置。

有关 Azure Defender for SQL 定价的详细信息，请参阅 [Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="enable-azure-defender"></a>启用 Azure Defender 
可通过多种方式启用 Azure Defender 计划。 可通过以下方式在订阅级别启用（**建议**）：

- [Azure 安全中心](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [使用 REST API、Azure CLI、PowerShell 或 Azure Policy 以编程方式启用](#enable-azure-defender-plans-programatically)

或者，可以根据[在资源级别为 Azure SQL 数据库启用 Azure Defender](#enable-azure-defender-for-azure-sql-database-at-the-resource-level) 中所述在资源级别启用它。

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>在 Azure 安全中心的订阅级别为 Azure SQL 数据库启用 Azure Defender
若要在 Azure 安全中心内的订阅级别为 Azure SQL 数据库启用 Azure Defender：

1. 在 [Azure 门户](https://portal.azure.com)中，打开“安全中心”。
1. 在安全中心的菜单中，选择“定价和设置”。
1. 选择相关订阅。
1. 将计划设置更改为“打开”。

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="在订阅级别为 Azure SQL 数据库启用 Azure Defender。":::

1. 选择“保存”。


### <a name="enable-azure-defender-plans-programatically"></a>以编程方式启用 Azure Defender 计划 

Azure 的灵活性允许使用多种编程方法来启用 Azure Defender 计划。 

可使用以下任一方法来为订阅启用 Azure Defender： 

| 方法       | 说明                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API     | [定价 API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security 定价](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [捆绑定价](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>在资源级别为 Azure SQL 数据库启用 Azure Defender

建议在订阅级别启用 Azure Defender 计划，这有助于创建不受保护的资源。 但是，如果你的组织有理由要在服务器级别启用 Azure Defender，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开你的服务器或托管实例。
1. 在“安全性”标题下，选择“安全中心” 。
1. 选择“启用 Azure Defender for SQL”。

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="在 Azure SQL 数据库中启用 Azure Defender for SQL。":::

> [!NOTE]
> 系统会自动创建一个存储帐户用于存储 **漏洞评估** 的扫描结果。 如果已为同一个资源组和区域中的另一台服务器启用 Azure Defender，则使用现有的存储帐户。
>
> Azure Defender 的成本遵循每个节点的 Azure 安全中心标准层级定价，其中节点是整个服务器或托管实例。 因此，只需支付一次即可使用 Azure Defender 保护服务器或托管实例上的所有数据库。 你可以从免费试用版开始试用 Azure Defender。


## <a name="manage-azure-defender-settings"></a>管理 Azure Defender 设置

查看和管理 Azure Defender 设置：

1. 在服务器或托管实例的“安全”区域中，选择“安全中心” 。

    在此页上，你将看到 Azure Defender for SQL 的状态：

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="在 Azure SQL 数据库中检查 Azure Defender for SQL 的状态。":::

1. 如果启用了 Azure Defender for SQL，则将看到一个“配置”链接，如上图所示。 若要编辑 Azure Defender for SQL 的设置，请选择“配置”。

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Azure Defender for SQL 的设置。":::

1. 进行必要的更改并选择“保存”。


## <a name="next-steps"></a>后续步骤

- 详细了解[漏洞评估](sql-vulnerability-assessment.md)
- 详细了解[高级威胁防护](threat-detection-configure.md)
- 了解有关 [Azure 安全中心](../../security-center/security-center-introduction.md)的详细信息