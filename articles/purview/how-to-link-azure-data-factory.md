---
title: 连接到 Azure 数据工厂
description: 本文介绍如何连接 Azure 数据工厂和 Azure Purview 来跟踪数据世系。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 40b1652bec99844675e030cfd12ef8ff28a0f01f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435449"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>如何连接 Azure 数据工厂和 Azure Purview

本文档介绍将 Azure 数据工厂帐户与 Azure Purview 帐户连接以跟踪数据世系所需的步骤。 本文档还详细介绍了覆盖范围和受支持的世系模式。

## <a name="view-existing-data-factory-connections"></a>查看现有的数据工厂连接

多个 Azure 数据工厂可连接到单个 Azure Purview 来推送世系信息。 当前限制允许从 Purview 管理中心一次最多连接十个数据工厂帐户。 若要显示已连接到 Purview 帐户的数据工厂帐户列表，请执行以下操作：

1. 在左侧导航窗格中选择“管理”。
2. 在“世系连接”下，选择“数据工厂” 。
3. 随即会显示数据工厂连接列表。

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="屏幕截图显示数据工厂连接列表。" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 请注意各种连接状态值：

    - **已连接**：数据工厂已连接到 Purview 帐户。
    - **已断开连接**：数据工厂有权访问该目录，但它已连接到其他目录。 因此，不会自动将数据世系报告给该目录。
    - **CannotAccess**：当前用户无法访问数据工厂，因此连接状态未知。

>[!Note]
>若要查看数据工厂连接，你需要具备以下角色。 不支持从管理组继承角色。
>- 对于在 2021 年 8 月 18 日或之后创建的 Purview 帐户：根集合的“集合管理员”角色 。
>- 对于在 2021 年 8 月 18 日之前创建的 Purview 帐户：Azure 内置的“所有者”、“参与者”、“读取者”或“用户访问管理员”角色    。

## <a name="create-new-data-factory-connection"></a>创建新的数据工厂连接

>[!Note]
>若要添加或删除数据工厂连接，你需要具备以下角色。 不支持从管理组继承角色。
>- 对于在 2021 年 8 月 18 日或之后创建的 Purview 帐户：根集合的“集合管理员”角色 。
>- 对于在 2021 年 8 月 18 日之前创建的 Purview 帐户：“所有者”或“用户访问管理员”角色  。 
>
> 此外，用户还需要是数据工厂的“所有者”或“参与者”。 

按照以下步骤将现有数据工厂连接到 Purview 帐户。 还可[从 ADF 将数据工厂连接到 Purview 帐户](../data-factory/connect-data-factory-to-azure-purview.md)。

1. 在左侧导航窗格中选择“管理”。
2. 在“世系连接”下，选择“数据工厂” 。
3. 在“数据工厂连接”页上，选择“新建” 。

4. 从列表中选择数据工厂帐户，然后选择“确定”。 此外，还可以按订阅名称进行筛选，以限制列表。

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="屏幕截图显示如何连接 Azure 数据工厂。" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    如果数据工厂已连接到当前的 Purview 帐户，或者数据工厂没有托管标识，则可能会禁用某些数据工厂实例。

    如果任何选定的数据工厂已连接到其他 Purview 帐户，则将显示一条警告消息。 选择“确定”，将断开与其他 Purview 帐户的数据工厂连接。 无需额外确认。

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="屏幕截图显示断开 Azure 数据工厂连接时的警告。" lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>现在支持一次最多添加 10 个数据工厂。 如果希望一次添加 10 个以上的数据工厂，请提交支持票证。

### <a name="how-authentication-works"></a>身份验证的工作原理

数据工厂的托管标识用于对从数据工厂到 Purview 的世系数据推送操作进行身份验证。 在将数据工厂连接到 UI 上的 Purview 时，会自动添加角色分配。 

- 对于在 2021 年 8 月 18 日或之后创建的 Purview 帐户，向数据工厂的托管标识授予 Purview 根集合的“数据管理者”角色  。 详细了解 [Azure Purview 中的访问控制](../purview/catalog-permissions.md)和[添加角色并限制通过集合的访问](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)。

- 对于在 2021 年 8 月 18 日之前创建的 Purview 帐户，向数据工厂的托管标识授予 Purview 帐户的 Azure 内置 [Purview 数据管理者](../role-based-access-control/built-in-roles.md#purview-data-curator)角色 。 详细了解 [Azure Purview 中的访问控制 - 旧版权限](../purview/catalog-permissions.md#legacy-permission-guide)。

### <a name="remove-data-factory-connections"></a>删除数据工厂连接

若要删除数据工厂连接，请执行以下操作：

1. 在“数据工厂连接”页上，选择一个或多个数据工厂连接旁边的“删除”按钮 。
2. 在弹出窗口中选择“确认”，以删除选定的数据工厂连接。

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="屏幕截图显示如何选择数据工厂以删除连接。" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="supported-azure-data-factory-activities"></a>支持的 Azure 数据工厂活动

Azure Purview 从以下 Azure 数据工厂活动捕获运行时世系：

- [复制数据](../data-factory/copy-activity-overview.md)
- [数据流](../data-factory/concepts-data-flow-overview.md)
- [执行 SSIS 包](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> 如果源或目标使用不受支持的数据存储系统，则 Azure Purview 将删除世系。

如以下各节部分所述，数据工厂与 Purview 之间的集成仅支持数据工厂支持的部分数据系统。

[!INCLUDE[data-factory-supported-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

### <a name="execute-ssis-package-support"></a>执行 SSIS 包支持

请参阅[支持的数据存储](how-to-lineage-sql-server-integration-services.md#supported-data-stores)。

## <a name="access-secured-azure-purview-account"></a>访问受保护的 Azure Purview 帐户
      
如果你的 Purview 帐户受防火墙保护，请了解如何使数据工厂能够通过 Purview 专用终结点[访问受保护的 Purview 帐户](../data-factory/how-to-access-secured-purview-account.md)。

## <a name="bring-data-factory-lineage-into-purview"></a>将数据工厂世系引入 Purview

有关端到端演练，请按照[教程：将数据工厂世系数据推送到 Azure Purview](../data-factory/turorial-push-lineage-to-purview.md) 进行操作。

## <a name="supported-lineage-patterns"></a>支持的世系模式

Azure Purview 支持多种世系模式。 生成的世系数据基于数据工厂活动中使用的源和接收器的类型。 虽然数据工厂支持 80 多种源和接收器，但其中仅部分受 Azure Purview 支持，如[支持的 Azure 数据工厂活动](#supported-azure-data-factory-activities)中所列。

若要配置数据工厂以发送世系信息，请参阅[世系入门](catalog-lineage-user-guide.md#get-started-with-lineage)。

在世系视图中查找信息的其他一些方法包括：

- 在“世系”选项卡中，将鼠标悬停在各个形状上，可在工具提示中预览有关资产的其他信息。
- 选择节点或边以查看其所属的资产类型或切换资产。
- 数据集的列显示在“世系”选项卡的左侧。有关列级世系的详细信息，请参阅[数据集列世系](catalog-lineage-user-guide.md#dataset-column-lineage)。

### <a name="data-lineage-for-11-operations"></a>1:1 操作的数据世系

捕获数据世系最常见的模式是将数据从单个输入数据集移动到单个输出数据集，两者之间包含一个进程。

下面是此模式的一个示例：

- 1 个源/输入：客户（SQL 表）
- 1 个接收器/输出：Customer1.csv (Azure Blob)
- 1 个进程：CopyCustomerInfo1\#Customer1.csv（数据工厂复制活动）

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="屏幕截图显示一对一数据工厂复制操作的世系。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>1:1 世系的数据移动（支持通配符）

捕获世系的另一个常见方案是使用通配符将文件从单个输入数据集复制到单个输出数据集。 借助通配符，复制活动可以使用文件名的共同部分匹配多个文件以进行复制。 Azure Purview 会捕获通过相应的复制活动复制的每个单独文件的文件级世系。

下面是此模式的一个示例：

* 源/输入：CustomerCall\*.csv（ADLS Gen2 路径）
* 接收器/输出：CustomerCall\*.csv（Azure blob 文件）
* 1 个进程：CopyGen2ToBlob\#CustomerCall.csv（数据工厂复制活动）  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="屏幕截图显示支持通配符的一对一复制操作的世系。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>n:1 世系的数据移动

你可以使用数据流活动来执行合并、联接等数据操作。 可以使用多个源数据集来生成目标数据集。 在此示例中，Azure Purview 会将单个输入文件的文件级世系捕获到作为数据流活动一部分的 SQL 表中。

下面是此模式的一个示例：

* 2 个源/输入：Customer.csv、Sales.parquet（ADLS Gen2 路径） 
* 1 个接收器/输出：公司数据（Azure SQL 表）
* 1 个进程：DataFlowBlobsToSQL（数据工厂数据流活动）

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="屏幕截图显示 n 对 1 的 A D F 数据流操作的世系。" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>资源集的世系

资源集是目录中的逻辑对象，代表基础存储中的许多分区文件。 有关详细信息，请参阅[了解资源集](concept-resource-sets.md)。 当 Azure Purview 从 Azure 数据工厂捕获世系时，它将应用规则来规范化各个分区文件并创建单个逻辑对象。

在以下示例中，Azure Data Lake Gen2 资源集是从 Azure Blob 生成的：

* 1 个源/输入：Employee\_management.csv (Azure Blob)
* 1 个接收器/输出：Employee\_management.csv (Azure Data Lake Gen 2)
* 1 个进程：CopyBlobToAdlsGen2\_RS（数据工厂复制活动）

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="屏幕截图显示资源集的世系。" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>后续步骤

[教程：将数据工厂世系数据推送到 Azure Purview](../data-factory/turorial-push-lineage-to-purview.md)

[目录世系用户指南](catalog-lineage-user-guide.md)

[链接到 Azure Data Share 以获取世系](how-to-link-azure-data-share.md)