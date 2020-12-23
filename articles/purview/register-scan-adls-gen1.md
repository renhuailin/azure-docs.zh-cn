---
title: 注册和扫描 Azure Data Lake Storage (ADLS) Gen1
description: 本教程介绍如何将数据从 Azure Data Lake Storage Gen1 扫描到 Azure 监控范围。
author: kchandra
ms.author: kchandra
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: ee0b9238deb7805113f0cbfa28d0b60a114820a9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552259"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>注册和扫描 Azure Data Lake Storage Gen1

本文概述了如何在 Azure 监控范围中将 Azure Data Lake Storage Gen1 注册为数据源，并在其中设置扫描。

> [!Note]
> Azure Data Lake Storage Gen2 现已正式发布。 我们建议你立即开始使用它。 有关详细信息，请参阅[产品页](https://azure.microsoft.com/services/storage/data-lake-storage/)。

## <a name="supported-capabilities"></a>支持的功能

Azure Data Lake Storage Gen1 数据源支持以下功能：

- 用于捕获 Azure Data Lake Storage Gen1 中的元数据和分类的 **完全和增量扫描**

- ADF 复制/数据流活动的数据资产之间的 **沿袭**

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
- 你需要成为 Azure 监控范围数据源管理员

## <a name="setting-up-authentication-for-a-scan"></a>设置扫描的身份验证

Azure Data Lake Storage Gen1 支持以下身份验证方法：

- 托管标识
- 服务主体

### <a name="managed-identity-recommended"></a>建议 (托管标识) 

为了便于安全，你可能想要使用监控范围 MSI 向你的数据存储进行身份验证。

若要使用数据目录的 MSI 设置扫描，必须首先向监控范围帐户授予扫描数据源的权限。 在设置扫描 *之前* ，必须完成此步骤，否则扫描将会失败。

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>将数据目录 MSI 添加到 Azure Data Lake Storage Gen1 帐户

你可以在订阅、资源组或资源级别添加目录的 MSI，具体取决于你希望它对其具有扫描权限的内容。

> [!Note]
> 你必须是订阅的所有者，才能在 Azure 资源上添加托管标识。

1. 在 [Azure 门户](https://portal.azure.com)中，查找订阅、资源组或资源 (例如，你希望允许目录扫描的 Azure Data Lake Storage Gen1 存储) 帐户。

2. 单击 "**概述**"，然后选择 "**数据资源管理器**"

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="选择访问控制":::

3. 单击顶部导航栏上的 " **访问** "

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="单击 &quot;访问&quot;":::

4. 单击“添加”。 在选择用户或组选择中添加 **监控范围目录** 。 选择 " **读取** " 和 " **执行** " 权限。 请确保在 "添加到" 选项中选择 "**此文件夹和所有子文件夹**"，如以下屏幕截图所示，然后单击 **"确定" "** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI 身份验证详细信息":::"

5. 如果密钥保管库尚未连接到监控范围，则需要 [创建新的 key vault 连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)。

6. 最后，使用服务主体 [创建新的凭据](manage-credentials.md#create-a-new-credential) 来设置扫描
> [!Note]
> 将目录的 MSI 添加到数据源后，在设置扫描之前，请等待15分钟以传播权限。

大约15分钟后，目录应具有相应的权限，以便能够)  (的资源。

### <a name="service-principal"></a>服务主体

若要使用服务主体，必须先创建以下步骤之一：

1. 导航到 [Azure 门户](https://portal.azure.com)。

2. 从左侧菜单中选择 " **Azure Active Directory** "。

3. 选择 **“应用注册”**。

4. 选择“+ 新建应用程序注册”。

5. 输入 **应用程序** 的名称 (服务主体名称) 。

6. 选择“仅此组织目录中的帐户”。

7. 对于 " **重定向 URI** "，请选择 " **Web** " 并输入所需的任何 URL;它不一定是真实的或工作的。

8. 然后选择“注册”。

9. 复制显示名称和应用程序 ID 中的值。

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>将数据目录服务主体添加到 Azure Data Lake Storage Gen1 帐户
1. 在 [Azure 门户](https://portal.azure.com)中，查找订阅、资源组或资源 (例如，你希望允许目录扫描的 Azure Data Lake Storage Gen1 存储) 帐户。

2. 单击 "**概述**"，然后选择 "**数据资源管理器**"

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="选择访问控制":::

3. 单击顶部导航栏上的 " **访问** "

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="单击 &quot;访问&quot;":::

4. 单击“添加”。 在选择用户或组选择中添加 **服务主体应用程序** 。 选择 " **读取** " 和 " **执行** " 权限。 请确保在 "添加到" 选项中选择 **此文件夹和所有子文件夹**，如以下屏幕截图所示，然后单击 **"确定"** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="服务主体身份验证详细信息":::

5. 如果密钥保管库尚未连接到监控范围，则需要 [创建新的 key vault 连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)。

6. 最后，使用服务主体 [创建新的凭据](manage-credentials.md#create-a-new-credential) 来设置扫描。

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>注册 Azure Data Lake Storage Gen1 数据源

若要在数据目录中注册新的 ADLS Gen1 帐户，请执行以下操作：

1. 导航到监控范围数据目录。
2. 在左侧导航栏中选择 " **源** "。
3. 选择“注册”
4. 在 " **注册源**" 中，选择 **Azure Data Lake Storage Gen1**。 
5. 选择“继续”。 

在 "注册源" (Azure Data Lake Storage Gen1) "屏幕上，执行以下操作：

1. 输入将在目录中列出的数据源的 **名称** 。
2. 选择订阅以筛选存储帐户
3. 选择存储帐户
4. 选择一个集合，或创建一个新的集合 (可选) 
5. 完成注册数据源。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 监控范围数据目录](how-to-browse-catalog.md)
- [搜索 Azure 监控范围数据目录](how-to-search-catalog.md)
