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
ms.date: 08/17/2021
ms.author: memildin
ms.openlocfilehash: 86edda1fa9aff73a5f6daca1494a81ab94aa9e3b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322906"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Azure 安全中心中的 SQL 信息保护策略
 
SQL 信息保护的[数据发现和分类机制](../azure-sql/database/data-discovery-and-classification-overview.md)提供了用于发现、标记和报告数据库中的敏感数据以及对其进行分类的高级功能。 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)、[Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)以及 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 中均内置了此机制。

分类机制基于以下两个元素：

- 标签 - 这是主要的分类属性，用于定义存储在列中的数据的敏感度级别。 
- 信息类型 - 为列中存储的数据的类型提供其他粒度。

安全中心中的信息保护策略选项提供了一组预定义的标签和信息类型，这些标签和信息类型充当分类引擎的默认值。 可以根据组织的需要自定义策略，如下所述。

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="此页显示 SQL 信息保护策略。":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>如何访问 SQL 信息保护策略？

可通过三种方法来访问信息保护策略：

- （推荐）通过安全中心的“定价和设置”页访问
- 通过安全建议“应对 SQL 数据库中的敏感数据进行分类”访问
- 通过 Azure SQL DB 数据发现页访问

每个选项都显示在以下相关选项卡中。



### <a name="from-security-centers-settings"></a>[通过安全中心的设置](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>通过安全中心的“定价和设置”页访问策略<a name="sqlip-tenant"></a>

在安全中心的“定价和设置”页，选择“SQL 信息保护” 。

> [!NOTE]
> 此选项仅对具有租户级别权限的用户显示。 [向你自己授予租户范围的权限](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)。

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="通过 Azure 安全中心的“定价和设置”页访问 SQL 信息保护策略。":::



### <a name="from-security-centers-recommendation"></a>[通过安全中心的建议](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>通过安全中心建议访问策略<a name="sqlip-db"></a>

使用安全中心的建议“应对 SQL 数据库中的敏感数据进行分类”查看数据库的“数据发现和分类”页。 在这里，你还将看到发现的包含我们建议你对其进行分类的信息的列。

1. 在安全中心的“建议”页上，搜索建议“应对 SQL 数据库中的敏感数据进行分类” 。

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="查找用于访问 SQL 信息保护策略的建议。":::

1. 在建议详细信息页上，从“运行正常”或“运行不正常”选项卡中选择数据库 。

1. 此时将打开“数据发现和分类”页。 选择“配置” 。

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="通过 Azure 安全中心中的相关建议打开 SQL 信息保护策略":::



### <a name="from-azure-sql"></a>[通过 Azure SQL](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>通过 Azure SQL 访问策略<a name="sqlip-azuresql"></a>

1. 在 Azure 门户中打开 Azure SQL。

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="在 Azure 门户中打开 Azure SQL。":::

1. 选择任意数据库。

1. 在菜单的“安全”区域中，打开“数据发现和分类”页 (1) 并选择“配置”(2)  。

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="在 Azure SQL 中打开 SQL 信息保护策略。":::

--- 


## <a name="customize-your-information-types"></a>自定义信息类型

若要管理和自定义信息类型，请执行以下操作：

1. 选择“管理信息类型”。

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="管理信息保护策略的信息类型。":::

1. 若要添加新类型，请选择“创建信息类型”。 可以为信息类型配置名称、描述和搜索模式字符串。 搜索模式字符串可以选择使用带有通配符的关键字（使用字符 %），自动发现引擎根据列的元数据使用该关键字来识别数据库中的敏感数据。
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="为信息保护策略配置新的信息类型。":::

1. 还可以通过添加其他搜索模式字符串、禁用某些现有字符串或更改描述来修改内置类型。 

    > [!TIP]
    > 无法删除内置类型或更改其名称。 

1. 信息类型按发现排名升序列出，这意味着列表中排名较高的类型将先尝试匹配。 要更改信息类型之间的排名，请将类型拖动到表格中的正确位置，或使用“上移”和“下移”按钮更改顺序。 

1. 完成后，请选择“确定”。

1. 完成信息类型管理后，请确保关联相关类型和相关标签，方法是单击特定标签的“配置”，然后根据需要添加或删除信息类型。

1. 若要应用更改，请选择主“标签”页中的“保存” 。
 

## <a name="exporting-and-importing-a-policy"></a>导出和导入策略

可以下载包含定义的标签和信息类型的 JSON 文件，在选择的编辑器中编辑该文件，然后导入更新后的文件。 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="导出和导入信息保护策略。":::

> [!NOTE]
> 需要租户级别的权限才能导入策略文件。 


## <a name="permissions"></a>权限

要自定义 Azure 租户的信息保护策略，需要对租户的根管理组执行以下操作：
  - Microsoft.Security/informationProtectionPolicies/read
  - Microsoft.Security/informationProtectionPolicies/write 

有关详细信息，请参阅[授予和请求租户范围的可见性](tenant-wide-permissions-management.md)。

## <a name="manage-sql-information-protection-using-azure-powershell"></a>使用 Azure PowerShell 管理 SQL 信息保护

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy)：检索有效的租户 SQL 信息保护策略。
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy)：设置有效的租户 SQL 信息保护策略。
 

## <a name="next-steps"></a>后续步骤
 
本文介绍了如何在 Azure 安全中心中定义信息保护策略。 若要详细了解如何使用 SQL 信息保护对 SQL 数据库中的敏感数据进行分类和保护，请参阅 [Azure SQL 数据库数据发现和分类](../azure-sql/database/data-discovery-and-classification-overview.md)。

有关安全中心中的安全策略和数据安全性的详细信息，请参阅以下文章：
 
- [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md)：了解如何配置 Azure 订阅和资源组的安全策略
- [Azure 安全中心的数据安全](security-center-data-security.md)：了解安全中心如何管理数据并确保数据安全性
