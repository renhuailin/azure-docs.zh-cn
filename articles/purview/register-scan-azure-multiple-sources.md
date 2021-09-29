---
title: 在 Azure Purview 中扫描多个源
description: 了解如何扫描 Azure Purview 数据目录中的整个 Azure 订阅或资源组。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: d141ac2be690a0cb788285a773208e0d930f5e67
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209882"
---
# <a name="register-and-scan-multiple-sources-in-azure-purview"></a>在 Azure Purview 中注册和扫描多个源

本文概述了如何在 Azure Purview 中注册多个源（Azure 订阅或资源组）并对齐设置扫描。

## <a name="supported-capabilities"></a>支持的功能

你可以通过扫描多个源来捕获 Azure Purview 支持的大多数 Azure 资源类型的元数据和架构。 Azure Purview 会根据系统分类规则和自定义分类规则自动分类数据。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure Purview 帐户。 有关详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 请确保你是 Azure Purview 的数据源管理员。 你还必须是所有者或用户访问管理员，才能在订阅或资源组中添加角色。
- 按以下部分所述设置身份验证。

### <a name="set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>为枚举订阅或资源组下的资源设置身份验证

1. 在 Azure 门户中，前往订阅或资源组。  
1. 从左侧菜单中选择“访问控制(IAM)” **** 。  
1. 选择“+添加”  。 
1. 在“选择输入”框中选择“读取者”角色，然后输入 Azure Purview 帐户名称（表示其 MSI 文件名） 。 
1. 选择“保存”以完成角色分配。

### <a name="set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>为扫描订阅或资源组下的资源设置身份验证

在 Azure 中为多个源设置身份验证有两种方式：

- 托管标识
- 服务主体

必须为订阅或资源组中你要注册和扫描的每个资源设置身份验证。 Azure 存储资源类型（Azure Blob 存储和 Azure Data Lake Storage Gen2）允许在订阅或资源组级别添加 MSI 文件或服务主体作为存储 blob 数据阅读器，从而使之变得简单。 然后，这些权限会下沉到该订阅或资源组中的每个存储帐户。 对于所有其他资源类型，必须在每个资源上应用 MSI 文件或服务主体，或者创建脚本来完成此操作。 

要了解如何在订阅或资源组中的每种资源类型上添加权限，请参阅以下资源：
    
- [Azure Blob 存储](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL 数据库](register-scan-azure-sql-database.md)
- [Azure SQL 托管实例](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-multiple-sources"></a>注册多个源

要在数据目录中注册新的多个源，请执行以下操作：

1. 转到 Azure Purview 帐户。
1. 在左侧菜单中选择“数据映射”。
1. 选择“注册”。
1. 在“注册源”上，选择“Azure (多源)” 。

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="屏幕截图显示屏幕上用于注册多个源的 Azure (多源) 的磁贴。":::
1. 选择“继续”。
1. 在“注册源(Azure)”屏幕上，执行以下操作：

   1. 在“名称”框中，输入在目录中列出数据源时将使用的名称。 
   1. 在“管理组”框中，可以选择要下沉到的的管理组。
   1. 在“订阅”和“资源组”下拉列表框中，分别选择订阅或特定的资源组 。 注册范围将设置为所选订阅或资源组。  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="屏幕截图显示用于选择订阅和资源组的框。":::
   1. 在“选择集合”框中，选择一个集合，或创建一个新的集合（可选）。
   1. 选择“注册”以注册数据源。


## <a name="create-and-run-a-scan"></a>创建并运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。
1. 选择所注册的数据源。
1. 选择“查看详细信息” > “+ 新建扫描”，或使用源磁贴上的扫描快速操作图标  。
1. 对于“名称”，请填写名称。
1. 对于“类型”，请选择要在此源中扫描的资源类型。 选择以下选项之一：

    - 保持选择“全部”。 此选择包括该订阅或资源组中当前可能不存在的未来资源类型。
    - 使用框专门选择要扫描的资源类型。 如果选择此选项，则扫描时不包括可能会在此订阅或资源组中创建的未来资源类型，除非在将来对扫描进行显式编辑。
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="屏幕截图显示用于扫描多个源的选项。":::

1. 选择凭据以连接到数据源中的资源： 
    - 你可以选择父级凭据作为 MSI 文件，也可以选择适用于特定服务主体类型的凭据。 然后，可以将该凭据用于订阅或资源组下的所有资源类型。
    - 可以专门选择资源类型，并为该资源类型应用不同的凭据。
    
    每个凭据都会被视为用于特定类型的所有资源的身份验证方法。 必须对资源设置所选凭据才能成功扫描这些资源，如[本文前面部分](#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)所述。
1. 在每个类型中，你可以选择扫描所有资源，也可以选择按名称扫描部分资源：
    - 如果将此选项保留为“全部”，则在将来运行扫描时也会扫描该类型的未来资源。
    - 如果选择特定的存储帐户或 SQL 数据库，则扫描不包括在此订阅或资源组中创建的该类型的未来资源，除非在将来对扫描进行显式编辑。
 
1. 选择“继续”以继续。 Azure Purview 会测试访问权限，以检查你是否以读取者的身份将 Azure Purview MSI 文件应用于订阅或资源组。 如果收到错误消息，请按照[这些说明](#set-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)进行解决。

1. 为在上一步选择的每种资源类型选择扫描规则集。 还可以创建内联扫描规则集。
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="屏幕截图显示每种资源类型的扫描规则。":::

1. 选择扫描触发器。 可以将其计划为每周或每月运行一次，或者只运行一次。

1. 查看扫描，然后选择“保存”以完成设置。 

## <a name="view-your-scans-and-scan-runs"></a>查看扫描和扫描运行情况

1. 通过选择“数据映射”部分的磁贴上的“查看详细信息”，查看源详细信息 。 

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="屏幕截图显示源详细信息。"::: 

1. 转到“扫描详细信息”页来查看扫描运行详细信息。
   
    状态栏是有关子资源运行状态的简短摘要。 它显示在订阅级别或资源组级别。 这些颜色具有以下含义：
    
    - 绿色：扫描成功。
    - 红色：扫描失败。 
    - 灰色：扫描仍在进行中。
   
    你可以选择每个扫描以查看更详细的信息。

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="屏幕截图显示扫描的详细信息。":::

1. 在源详细信息底部查看最近失败的扫描运行摘要。 还可以查看有关这些运行情况的更详细的信息。

## <a name="manage-your-scans-edit-delete-or-cancel"></a>管理扫描：编辑、删除或取消
要管理扫描，请执行以下操作：

1. 转到管理中心。
1. 在“源和扫描”部分下选择“数据源”，然后选择所需的数据源 。
1. 选择要管理的扫描。 然后： 

   - 可以通过选择“编辑”来编辑扫描。
   - 可以通过选择“删除”来删除扫描。
   - 如果扫描正在运行，则可以通过选择“取消”来取消扫描。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)    
