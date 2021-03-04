---
title: 如何扫描 Azure 多个源
description: 了解如何在 Azure 监控范围数据目录中扫描整个 Azure 订阅或资源组。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123558"
---
# <a name="register-and-scan-azure-multiple-sources"></a>注册并扫描 Azure 多个源

本文概述了如何在监控范围中注册 Azure 多源 (Azure 订阅或资源组) 并在其上设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure 多源支持扫描，以便捕获监控范围支持的大多数 Azure 资源类型的元数据和架构。 它还根据系统和自定义分类规则自动分类数据。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
- 你需要成为 Azure 监控范围数据源管理员
- 设置身份验证，如以下部分所述

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>设置用于枚举订阅或资源组下的资源的身份验证

1. 导航到 Azure 门户中的订阅或资源组。  
1. 从左侧导航菜单中选择 " **访问控制 (IAM)**   
1. 您必须是所有者或用户访问管理员才能在订阅或资源组上添加角色。 选择 " *+ 添加* " 按钮。 
1. 设置 " **读者** " 角色，并在 "选择输入框" 下输入代表其 MSI) 的 Azure 监控范围帐户名 (。 单击 " *保存* " 完成角色分配。

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>设置身份验证以扫描订阅或资源组下的资源

可通过两种方式为 Azure 多源设置身份验证：

- 托管标识
- Service Principal

> [!NOTE]
> 必须对订阅或资源组中要注册和扫描的每个资源设置身份验证。 Azure blob 存储和) Azure Data Lake Storage Gen2 (的 azure 存储资源类型使你能够在订阅/资源组级别将 MSI 或服务主体添加为存储 blob 数据读取器，使你可以轻松地进行添加。然后，这些权限会滴到该订阅或资源组中的每个存储帐户。 对于所有其他资源类型，您必须在每个资源上应用 MSI 或服务主体，或在设备上应用一个脚本来执行此操作。 下面介绍了如何在订阅或资源组中的每种资源类型上添加权限。
    
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
1. 在 " **注册源**" 中，选择 " **Azure (多个)**
1. 选择“继续”

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="注册 Azure 多源":::

在 " **注册源 (Azure 多)** " 屏幕上，执行以下操作：

1. 输入将在目录中列出的数据源的 **名称** 
1. （可选）选择要筛选到的 **管理组**
1. 在下拉列表中，选择给定订阅下的 **订阅或特定资源组**。 注册范围将设置为所选订阅或资源组  
1. 选择一个 **集合** ，或创建一个新的集合 (可选) 
1. **完成** 以注册数据源

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="设置 Azure 多源":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 导航到 " **源** " 部分

1. 选择已注册的数据源

1. 单击 "查看详细信息" 并选择 " **+ 新扫描** " 或 "使用源磁贴上的扫描快速操作" 图标

1. 填写 *名称* ，然后选择要在此源中扫描的所有类型的资源

    1. 您可以将其保留为 *所有* (这包括将来可能不存在于该订阅或资源组中的资源类型) 
    1. 您可以 **专门选择** 要扫描的资源类型。 如果选择此选项，则将来可能会在此订阅或资源组中创建的资源类型将不包含在扫描中，除非将来显式编辑扫描
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure 多源扫描":::

1. 选择凭据以连接到数据源中的资源。 
    1. 你可以选择作为 MSI 的 **父级别的凭据** ，也可以选择特定的服务主体类型凭据，你可以选择对订阅或资源组下的所有资源类型使用该凭据
    1. 你还可以专门 **选择资源类型，并** 为该资源类型应用不同的凭据
    1. 每个凭据将被视为针对特定类型的所有资源进行身份验证的方法
    1. 必须对资源设置所选的凭据才能成功扫描这些资源，如以上 [部分](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) 所述
1. 在每个类型中，你可以选择扫描所有资源，也可以选择按名称扫描它们的子集。
    1. 如果保留该选项，则将来的扫描运行中也将扫描该类型的 **所有** 资源，
    1. 如果选择特定存储帐户或 SQL 数据库，则不会包括在此订阅或资源组中为该类型创建的将来资源用于扫描，除非将来显式编辑扫描
 
1.  单击“继续”以继续。 我们将测试访问权限，以检查是否已将监控范围 MSI 作为订阅或资源组的读取器应用。 如果引发错误消息，请按照[此处](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)的说明进行操作

1.  为在上一步中选择的每种资源类型选择 " **扫描规则集** "。 你还可以创建内联扫描规则集。
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Azure 多重扫描规则集选择":::

1. 选择扫描触发器。 可以将其计划为每周运行一次或 **每月** 运行 **一次**

1. 查看扫描，然后选择 "保存" 以完成设置   

## <a name="viewing-your-scans-and-scan-runs"></a>查看扫描和扫描运行

1. 通过单击 "源" 部分下的磁贴上的 " **查看详细信息** " 查看源详细信息。 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Azure 多源详细信息"::: 

1. 通过导航到 **扫描详细信息** 页来查看扫描运行详细信息。
    1. *状态栏* 是有关子资源运行状态的简短摘要。 它将显示在订阅或资源组级别
    1. 绿色表示成功，红色表示失败。 灰色表示扫描仍在进行中
    1. 你可以单击每个扫描以查看更精细的详细信息

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Azure 多扫描详细信息":::

1. 查看 "源详细信息" 页底部的 "最近失败的扫描" 运行的摘要。 您还可以单击以查看有关这些运行的更详细信息。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>管理扫描 - 编辑、删除或取消
若要管理或删除扫描，请执行以下操作：

- 导航到管理中心。 选择 "源和扫描" 部分下的 "数据源"，然后选择所需的数据源

- 选择要管理的扫描。 可以通过选择 "编辑" 来编辑扫描

- 可以通过选择 "删除" 来删除扫描
- 如果扫描正在运行，也可以取消该扫描

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)    
