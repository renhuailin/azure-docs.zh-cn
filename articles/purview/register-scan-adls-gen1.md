---
title: 注册和扫描 Azure Data Lake Storage (ADLS) Gen1
description: 本教程介绍如何将 Azure Data Lake Storage Gen1 中的数据扫描到 Azure Purview 中。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 1c4801814e77efdb681f32ea35d4dfb68618900b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736370"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>注册和扫描 Azure Data Lake Storage Gen1

本文简要介绍如何将 Azure Data Lake Storage Gen1 注册为 Azure Purview 中的数据源并对其设置扫描。

> [!Note]
> Azure Data Lake Storage Gen2 现已正式发布。 我们建议你立即开始使用它。 有关详细信息，请参阅[产品页](https://azure.microsoft.com/services/storage/data-lake-storage/)。

## <a name="supported-capabilities"></a>支持的功能

Azure Data Lake Storage Gen1 数据源支持以下功能：

- 完全扫描和增量扫描，用于捕获 Azure Data Lake Storage Gen1 中的元数据和分类

- ADF 复制/数据流活动的数据资产之间的世系

对于 csv、tsv、psv、ssv 等文件类型，当满足以下逻辑时，系统将提取架构：

1. 第一行值非空
2. 第一行值是唯一的
3. 第一行值既不是日期也不是数字

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 你需要是 Azure Purview 数据源管理员

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

支持为 Azure Data Lake Storage Gen1 使用以下身份验证方法：

- 托管标识
- 服务主体

### <a name="managed-identity-recommended"></a>托管标识（推荐）

出于便利和安全原因，你可能想要使用 Purview MSI 向数据存储进行身份验证。

若要使用数据目录的 MSI 设置扫描，必须先向 Purview 帐户授予扫描数据源的权限。 必须在设置扫描之前执行此步骤，否则扫描将失败。

#### <a name="adding-the-purview-msi-to-an-azure-data-lake-storage-gen1-account"></a>将 Purview MSI 添加到 Azure Data Lake Storage Gen1 帐户

可在订阅、资源组或资源级别添加目录的 MSI，具体取决于你希望它对哪些内容具有扫描权限。

> [!Note]
> 你必须是订阅的所有者才能在 Azure 资源上添加托管标识。

1. 从 [Azure 门户](https://portal.azure.com)中，查找希望允许目录扫描的订阅、资源组或资源（例如 Azure Data Lake Storage Gen1 存储帐户）。

2. 单击“概述”，然后选择“数据资源管理器” 

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="选择访问控制":::

3. 单击顶部导航栏上的“访问”

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="单击 访问":::

4. 单击“添加”。 在“选择用户或组”选项中添加“Purview 目录”。 选择“读取”和“执行”权限 。 确保选择“此文件夹和所有子文件夹和“添加到”选项中的“访问权限项和默认权限项”，如下面的屏幕截图所示。 单击“确定”
   “MSI 身份验证详细信息”:::image type="content" source="./media/register-scan-adls-gen1/gen1-managed-service-identity-authentication.png" alt-text="_":::
   
> [!Tip]
> “访问权限项”是针对当前文件和文件夹的权限项。
> “默认权限项”是由新文件和文件夹继承的权限项。
> 
> 若要仅授予当前现有文件的权限，请选择“访问权限项”。
> 
> 若要授予扫描将来要添加的文件和文件夹的权限，则包含“默认权限项”。
> 
> 有关详细信息，请参阅[权限文档。](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders)

5. 如果密钥保管库尚未连接到 Purview，需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)。

6. 最后，使用服务主体[创建新凭据](manage-credentials.md#create-a-new-credential)以设置扫描

> [!Note]
> 在数据源上添加目录的 MSI 后，请等待 15 分钟使权限得以传播，然后再设置扫描。

大约 15 分钟后，目录会具有适当的权限，能够扫描资源。

### <a name="service-principal"></a>服务主体

若要使用服务主体，必须先按照以下步骤创建一个主体：

1. 导航到 [Azure 门户](https://portal.azure.com)。

2. 从左侧菜单中选择“Azure Active Directory”。

3. 选择 **“应用注册”**。

4. 选择“+ 新建应用程序注册”。

5. 为应用程序输入名称（服务主体名称）。

6. 选择“仅此组织目录中的帐户”。

7. 对于重定向 URI，请选择“Web”并输入所需的任何 URL；它不需要是真实的，也不需要有效 。

8. 然后选择“注册”。

9. 复制显示名称和应用程序 ID 中的值。

#### <a name="adding-the-purview-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>将 Purview 服务主体添加到 Azure Data Lake Storage Gen1 帐户

1. 从 [Azure 门户](https://portal.azure.com)中，查找希望允许目录扫描的订阅、资源组或资源（例如 Azure Data Lake Storage Gen1 存储帐户）。

2. 单击“概述”，然后选择“数据资源管理器” 

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="选择访问控制":::

3. 单击顶部导航栏上的“访问”

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="单击“访问”":::

4. 单击“添加”。 在“选择用户或组”选项中，添加服务主体应用程序。 选择“读取”和“执行”权限 。 确保选择“此文件夹和所有子文件夹和“添加到”选项中的“访问权限项和默认权限项”，如下面的屏幕截图所示。 单击“确定”
   “服务主体身份验证详细信息”:::image type="content" source="./media/register-scan-adls-gen1/gen1-service-principal-permissions.png" alt-text="service principal authentication details":::

> [!Tip]
> “访问权限项”是针对当前文件和文件夹的权限项。
> “默认权限项”是由新文件和文件夹继承的权限项。
>
> 若要仅授予当前现有文件的权限，请选择“访问权限项”。
>
> 若要授予扫描将来要添加的文件和文件夹的权限，则包含“默认权限项”。
>
> 有关详细信息，请参阅[权限文档。](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders)

5. 如果密钥保管库尚未连接到 Purview，需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)。

6. 最后，使用服务主体[创建一个新凭据](manage-credentials.md#create-a-new-credential)来设置扫描。

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>注册 Azure Data Lake Storage Gen1 数据源

若要在数据目录中注册新的 ADLS Gen1 帐户，请执行以下操作：

1. 导航到 Purview 数据目录。
2. 在左侧导航中选择“数据映射”。
3. 选择“注册”
4. 在“注册源”中，选择“Azure Data Lake Storage Gen1” 。 
5. 选择“继续”

在“注册源(Azure Data Lake Storage Gen1)”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
2. 选择你的订阅以筛选存储帐户。
3. 选择一个存储帐户。
4. 选择一个集合或创建新集合（可选）。
5. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-adls-gen1/register-sources.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择你注册的 Azure Data Lake Storage Gen1 源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。

   :::image type="content" source="media/register-scan-adls-gen1/set-up-scan-adls-gen1.png" alt-text="设置扫描":::

1. 可以选择列表中适当的项，以便将扫描范围限定在特定的文件夹和子文件夹。

   :::image type="content" source="media/register-scan-adls-gen1/gen1-scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-adls-gen1/select-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-adls-gen1/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
