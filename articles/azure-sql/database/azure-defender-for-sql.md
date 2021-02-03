---
title: Azure Defender for SQL
description: 了解用于管理数据库漏洞以及检测可能对 Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse 中数据库造成威胁的异常活动的功能。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508791"
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

可以通过 [Azure 门户](https://portal.azure.com)访问 Azure Defender。 通过导航到服务器或托管实例的“安全性”标题下的“安全中心”来启用 Azure Defender 。

> [!NOTE]
> 系统会自动创建一个存储帐户用于存储 **漏洞评估** 的扫描结果。 如果已为同一个资源组和区域中的另一台服务器启用 Azure Defender，则使用现有的存储帐户。
>
> Azure Defender 的成本遵循每个节点的 Azure 安全中心标准层级定价，其中节点是整个服务器或托管实例。 因此，只需支付一次即可使用 Azure Defender 保护服务器或托管实例上的所有数据库。 你可以从免费试用版开始试用 Azure Defender。

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="在 Azure SQL 数据库中启用适用于 SQL 的 Azure Defender":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>跟踪漏洞并调查威胁警报

单击“漏洞评估”卡，查看和管理漏洞扫描和报告，并跟踪安全状况。 如果收到安全警报，请单击“高级威胁防护”卡，查看警报的详细信息，并通过 Azure 安全中心安全警报页面查看 Azure 订阅中所有警报的综合报告。

## <a name="manage-azure-defender-settings"></a>管理 Azure Defender 设置

查看和管理 Azure Defender 设置的步骤：

1. 从服务器或托管实例的 " **安全** " 区域中，选择 " **安全中心**"。

    在此页上，你将看到适用于 SQL 的 Azure Defender 的状态：

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="在 Azure SQL 数据库中检查适用于 SQL 的 Azure Defender 的状态":::

1. 如果启用了 Azure Defender for SQL，你将看到如上图所示的 " **配置** " 链接。 若要编辑 Azure Defender for SQL 的设置，请选择 " **配置**"。

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="安全性服务器设置":::

1. 进行必要的更改，然后选择 " **保存**"。


## <a name="next-steps"></a>后续步骤

- 详细了解[漏洞评估](sql-vulnerability-assessment.md)
- 详细了解[高级威胁防护](threat-detection-configure.md)
- 了解有关 [Azure 安全中心](../../security-center/security-center-introduction.md)的详细信息