---
title: Azure 安全中心中的 SQL 信息保护策略
description: 了解如何在 Azure 安全中心中自定义信息保护策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013779"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Azure 安全中心中的 SQL 信息保护策略
 
SQL 信息保护的 [数据发现和分类机制](../azure-sql/database/data-discovery-and-classification-overview.md) 提供了高级功能，可用于发现、分类、标记和报告数据库中的敏感数据。 它内置于 [AZURE Sql 数据库](../azure-sql/database/sql-database-paas-overview.md)、 [azure Sql 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)和 [azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)中。

分类机制基于以下两个元素：

- **标签** -主要分类属性，用于定义列中存储 *数据的敏感度级别* 。 
- **信息类型** -为列中存储的 *数据类型* 提供了额外的粒度。

安全中心内的信息保护策略选项提供了一组预定义的标签和信息类型，用作分类引擎的默认值。 你可以根据组织的需求自定义策略，如下所述。

> [!IMPORTANT]
> 若要为你的 Azure 租户自定义信息保护策略，你将需要租户的根管理组的管理权限。 若要深入了解 [Azure 安全中心，请参阅获取租户范围的可见性](security-center-management-groups.md)。

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="显示 SQL 信息保护策略的页面":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>如何实现访问 SQL 信息保护策略吗？

有三种方法可以访问信息保护策略：

- **(建议)** 从安全中心的 "定价和设置" 页
- 根据安全建议，应将 SQL 数据库中的敏感数据分类
- 从 Azure SQL DB 数据发现页

以下各项显示在下面的相关选项卡中。



### <a name="from-security-centers-settings"></a>[**从安全中心的设置**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>从安全中心的 "定价和设置" 页访问策略 <a name="sqlip-tenant"></a>

在安全中心的 " **定价和设置** " 页中，选择 " **SQL 信息保护**"。

> [!NOTE]
> 此选项仅对具有租户级别权限的用户显示。 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="从 Azure 安全中心的 &quot;定价和设置&quot; 页访问 SQL 信息保护策略":::



### <a name="from-security-centers-recommendation"></a>[**从安全中心的建议**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>从安全中心建议访问策略 <a name="sqlip-db"></a>

使用安全中心的建议 "应该对 SQL 数据库中的敏感数据进行分类"，以便查看数据库的 "数据发现" 和 "分类" 页。 在这里，你还会看到发现的列包含我们建议你进行分类的信息。

1. 在安全中心的 " **建议** " 页上，搜索 **应分类的 SQL 数据库中的建议敏感数据**。

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="查找提供对 SQL 信息保护策略的访问的建议":::

1. 从 "建议详细信息" 页中，从 " **正常** " 或 "不 **正常** " 选项卡中选择数据库。

1. 此时将打开 " **数据发现 & 分类** " 页。 选择“配置” 。

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="从 Azure 安全中心的相关建议打开 SQL 信息保护策略":::



### <a name="from-azure-sql"></a>[**从 Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>从 Azure SQL 访问策略 <a name="sqlip-azuresql"></a>

1. 在 Azure 门户中，打开 Azure SQL。

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="从 Azure 门户打开 Azure SQL":::

1. 选择任何数据库。

1. 在菜单的 " **安全** " 区域中，打开 "& 分类" 页上的 " **数据发现** " (1) 并选择 " **配置** (2) "。

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="从 Azure SQL 打开 SQL 信息保护策略":::

--- 


## <a name="customize-your-information-types"></a>自定义信息类型

管理和自定义信息类型：

1. 选择 " **管理信息类型**"。

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="管理信息保护策略的信息类型":::

1. 若要添加新类型，请选择 " **创建信息类型**"。 你可以为信息类型配置名称、说明和搜索模式字符串。 搜索模式字符串可以选择使用带有通配符的关键字（使用字符 %），自动发现引擎根据列的元数据使用该关键字来识别数据库中的敏感数据。
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="为信息保护策略配置新的信息类型":::

1. 还可以通过添加其他搜索模式字符串、禁用一些现有字符串或更改说明来修改内置类型。 

    > [!TIP]
    > 不能删除或更改内置类型。 

1. 信息类型按发现排名升序列出，这意味着列表中排名较高的类型将先尝试匹配。 要更改信息类型之间的排名，请将类型拖动到表格中的正确位置，或使用“上移”和“下移”按钮更改顺序。 

1. 完成后，选择 **"确定"** 。

1. 完成信息类型管理后，请确保关联相关类型和相关标签，方法是单击特定标签的“配置”，然后根据需要添加或删除信息类型。

1. 若要应用更改，请在主 **标签** 页面中选择 "**保存**"。
 

## <a name="exporting-and-importing-a-policy"></a>导出和导入策略

你可以使用定义的标签和信息类型下载 JSON 文件，在所选编辑器中编辑该文件，然后导入更新的文件。 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="导出和导入信息保护策略":::

> [!NOTE]
> 你将需要租户级别的权限才能导入策略文件。 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>使用 Azure PowerShell 管理 SQL 信息保护

- [AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy)：检索有效的租户 SQL 信息保护策略。
- [AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy)：设置有效的租户 SQL 信息保护策略。
 

## <a name="next-steps"></a>后续步骤
 
本文介绍了如何在 Azure 安全中心定义信息保护策略。 若要详细了解如何使用 SQL 信息保护对 SQL 数据库中的敏感数据进行分类和保护，请参阅 [Azure SQL 数据库数据发现和分类](../azure-sql/database/data-discovery-and-classification-overview.md)。

有关安全中心安全策略和数据安全的详细信息，请参阅以下文章：
 
- [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md)：了解如何为 Azure 订阅和资源组配置安全策略
- [Azure 安全中心数据安全](security-center-data-security.md)：了解安全中心如何管理和保护数据
