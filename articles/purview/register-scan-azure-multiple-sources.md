---
title: 如何扫描 Azure 多源
description: 了解如何扫描 Azure Purview 数据目录中的整个 Azure 订阅或资源组。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123558"
---
# <a name="register-and-scan-azure-multiple-sources"></a>注册并扫描 Azure 多源

本文概述了如何在 Purview 中注册 Azure 多源（Azure 订阅或资源组）并在其上设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure 多源支持通过扫描来捕获 Purview 支持的大多数 Azure 资源类型的元数据和架构。 它还会根据系统的和自定义的分类规则自动分类数据。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 你需要是 Azure Purview 数据源管理员
- 按以下部分所示设置身份验证

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>为枚举订阅或资源组下的资源设置身份验证

1. 在 Azure 门户中，导航到订阅或资源组。  
1. 从左侧导航菜单中选择“访问控制(IAM)” ****   
1. 你必须是所有者或用户访问管理员，才能在订阅或资源组中添加角色。 选择“+ 添加”按钮。 
1. 设置“读取者”角色，并在“选择”输入框下输入 Azure Purview 帐户名称（表示其 MSI）。 单击“保存”以完成角色分配。

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>为扫描订阅或资源组下的资源设置身份验证

可通过两种方式为 Azure 多源设置身份验证：

- 托管标识
- Service Principal

> [!NOTE]
> 必须为订阅或资源组中你需要注册和扫描的每个资源设置身份验证。 Azure 存储资源类型（Azure Blob 存储和 Azure Data Lake Storage Gen2）允许你在订阅/资源组级别将 MSI 或服务主体添加为存储 Blob 数据读取器，从而简化操作。然后，将会逐级向该订阅或资源组中的每个存储帐户分配权限。 对于所有其他资源类型，你必须在每个资源上应用 MSI 或服务主体，或在设备上通过脚本来这样做。 下面介绍了如何在订阅或资源组中的每种资源类型上添加权限。
    
- [Azure Blob 存储](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL 数据库](register-scan-azure-sql-database.md)
- [Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>注册 Azure 多源

若要在数据目录中注册新的 Azure 多源，请执行以下操作：

1. 导航到你的 Purview 帐户
1. 在左侧导航区域中选择“源”
1. 选择“注册”
1. 在“注册源”上，选择“Azure (多源)”
1. 选择“继续”

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="注册 Azure 多源":::

在“注册源(Azure 多源)”屏幕上，执行以下操作：

1. 输入在目录中列出数据源时将使用的名称 
1. （可选）选择要筛选到的管理组
1. 在下拉列表中，选择一个订阅或选择给定订阅下的特定资源组。 注册范围将设置为所选订阅或资源组  
1. 选择一个集合或创建新集合（可选）
1. 选择“完成”以注册数据源

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="设置 Azure 多源":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 导航到“源”部分

1. 选择你已注册的数据源

1. 单击“查看详细信息”并选择“+ 新建扫描”，或使用源磁贴上的快速操作扫描图标

1. 在“名称”中进行填写，然后选择要在此源中扫描的所有资源类型

    1. 你可以将其保留为“所有”（这包括该订阅或资源组中当前可能不存在的未来资源类型）
    1. 你可以专门选择要扫描的资源类型。 如果你选择此选项，则扫描时不包括可能会在此订阅或资源组中创建的未来资源类型，除非你在将来对扫描进行显式编辑
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure 多源扫描":::

1. 选择用来连接到数据源中的资源的凭据。 
    1. 你可以在父级选择 MSI 作为凭据，也可以选择特定服务主体类型凭据，可以选择将该凭据用于订阅或资源组下的所有资源类型
    1. 你还可以专门选择资源类型，并为该资源类型应用不同的凭据
    1. 每个凭据都会被视为用于特定类型的所有资源的身份验证方法
    1. 你必须在资源上设置所选凭据才能成功扫描这些资源，如上面的[此部分](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)所述
1. 在每个类型中，你可以选择扫描所有资源，也可以选择按名称扫描部分资源。
    1. 如果将此选项保留为“所有”，则在将来运行扫描时也会扫描该类型的未来资源
    1. 如果你选择特定的存储帐户或 SQL 数据库，则扫描不包括在此订阅或资源组中创建的该类型的未来资源，除非在将来对扫描进行显式编辑
 
1.  单击“继续”以继续。 我们会测试访问权限，以检查是否已在订阅或资源组上将 Purview MSI 应用为读取器。 如果引发了错误消息，请按照[此处](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)的说明进行操作

1.  为在上一步选择的每种资源类型选择扫描规则集。 你还可以创建内联扫描规则集。
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Azure 多源扫描规则集选择":::

1. 选择扫描触发器。 可以将其计划为每周/每月运行，或只运行一次 

1. 查看扫描，然后选择“保存”以完成设置   

## <a name="viewing-your-scans-and-scan-runs"></a>查看扫描和扫描运行

1. 通过单击“源”部分的磁贴上的“查看详细信息”，查看源详细信息。 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Azure 多源详细信息"::: 

1. 导航到“扫描详细信息”页来查看扫描运行详细信息。
    1. 状态栏是有关子资源运行状态的简短摘要。 它将显示在订阅或资源组级别
    1. 绿色表示成功，而红色表示失败。 灰色表示扫描仍在进行中
    1. 可单击每个扫描以查看更精细的详细信息

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Azure 多源扫描详细信息":::

1. 在源详细信息页面底部查看最近失败的扫描运行摘要。 还可单击这些运行以查看与之相关的更精细的详细信息。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>管理扫描 - 编辑、删除或取消
若要管理或删除扫描，请执行以下操作：

- 导航到管理中心。 在“源和扫描”部分选择“数据源”，然后选择所需的数据源

- 选择要管理的扫描。 可以通过选择“编辑”来编辑扫描

- 可以通过选择“删除”来删除扫描
- 如果扫描正在运行，你也可以将其取消

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)    
