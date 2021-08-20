---
title: 大规模检查数据源就绪情况（预览）
description: 在本教程中，将在注册前验证 Azure 数据源的就绪情况，然后在 Azure Purview 中对其进行扫描。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 05/28/2021
ms.openlocfilehash: d7dc8ab7987f149747df30834f426ce6d119eb5c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105914"
---
# <a name="tutorial-check-data-source-readiness-at-scale-preview"></a>教程：大规模检查数据源就绪情况（预览）

> [!IMPORTANT]
> Azure Purview 目前处于预览阶段。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

若要扫描数据源，Azure Purview 需要访问这些数据源。 它使用凭据获取此访问权限。 凭据是可供 Azure Purview 用来对已注册的数据源进行身份验证的身份验证信息。 有几种方法设置 Azure Purview 凭据的方法，包括： 
- 分配到 Azure Purview 帐户的托管标识。
- Azure Key Vault 中存储的机密。 
- 服务主体。

在这个两部分的教程系列中，我们将帮助你大规模地为 Azure 订阅中的 Azure 数据源验证和配置所需 Azure 角色分配和网络访问。 然后，可以在 Azure Purview 中注册和扫描 Azure 数据源。 

部署 Azure Purview 帐户后运行 [Azure Purview 数据源就绪情况清单](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness)，然后在注册前扫描 Azure 数据源。 

在本教程系列的第 1 部分中，将执行以下操作：

> [!div class="checklist"]
>
> * 找到数据源，并准备一个数据源订阅列表。
> * 运行就绪情况清单脚本，在 Azure 中跨数据源配置任何缺失的基于角色的访问控制 (RBAC) 或网络配置。
> * 在输出报表中，查看 Azure Purview 托管标识 (MSI) 缺少的网络配置和角色分配。 
> * 与数据 Azure 订阅所有者共享报表，使其可以执行建议的操作。

## <a name="prerequisites"></a>必备条件

* 数据源所在的 Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure Purview 帐户](create-catalog-portal.md)。
* 每个订阅中的 Azure 密钥保管库资源，其中包含 Azure SQL 数据库、Azure Synapse Analytics 或 Azure SQL 托管实例等数据源。
* [Azure Purview 数据源就绪情况清单](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness)脚本。

> [!NOTE]
> Azure Purview 数据源就绪情况清单仅适用于 Windows。
> Azure Purview MSI 当前支持该就绪情况清单脚本。

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>为数据源准备 Azure 订阅列表

在运行该脚本之前，请创建一个包含四个列的 .csv 文件（例如 C:\temp\Subscriptions.csv）：

|列名称|说明|示例|
|----|----|----|
|`SubscriptionId`|数据源的 Azure 订阅 ID。|12345678-aaaa-bbbb-cccc-1234567890ab|
|`KeyVaultName`|部署在数据源订阅中的现有密钥保管库的名称。|ContosoDevKeyVault|
|`SecretNameSQLUserName`|包含 Azure Active Directory (Azure AD) 用户名的现有 Azure 密钥保管库机密的名称，可使用 Azure AD 身份验证登录到 Azure Synapse、Azure SQL 数据库或 Azure SQL 托管实例。|ContosoDevSQLAdmin|
|`SecretNameSQLPassword`|包含 Azure AD 用户密码的现有 Azure 密钥保管库机密的名称，可使用 Azure AD 身份验证登录到 Azure Synapse、Azure SQL 数据库或 Azure SQL 托管实例。|ContosoDevSQLPassword|
   

示例 .csv 文件：
    
:::image type="content" source="./media/tutorial-data-sources-readiness/subscriptions-input.png" alt-text="屏幕截图：显示示例订阅列表。" lightbox="./media/tutorial-data-sources-readiness/subscriptions-input.png":::

> [!NOTE] 
> 如果需要，可以更新代码中的文件名和路径。



## <a name="run-the-script-and-install-the-required-powershell-modules"></a>运行脚本并安装所需的 PowerShell 模块 

请按照以下步骤在 Windows 计算机上运行脚本：

1. 将 [Azure Purview 数据源就绪情况清单脚本下载](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness)到你选择的位置。

2. 在计算机上，在 Windows 任务栏的搜索框中输入 PowerShell。 在搜索列表中，右键单击“Windows PowerShell”，然后选择“以管理员身份运行”。

3. 在 PowerShell 窗口中输入以下命令。 （将 `<path-to-script>` 替换为提取的脚本文件的文件夹路径。）

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. 输入以下命令来安装 Azure cmdlet：

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
6. 如果看到提示“需要 NuGet 提供程序才能继续”，请输入“Y”，然后按“Enter”。

7. 如果看到提示“不受信任的存储库”，请输入“A”，然后按“Enter”。

5. 重复前面的步骤以安装 `Az.Synapse` 和 `AzureAD` 模块。

PowerShell 安装所需的模块可能最多需要一分钟时间。


## <a name="collect-other-data-needed-to-run-the-script"></a>收集运行脚本所需的其他数据

在运行 PowerShell 脚本以验证数据源订阅的就绪状态之前，请获取脚本中要使用的以下参数的值：

- `AzureDataType`：选择以下任一选项作为数据源类型，以检查你的订阅中数据类型的就绪情况： 
    
    - `BlobStorage`

    - `AzureSQLMI`

    - `AzureSQLDB`
    
    - `ADLSGen2`
    
    - `ADLSGen1`
    
    - `Synapse`
    
    - `All`

- `PurviewAccount`：现有的 Azure Purview 帐户资源名称。

- `PurviewSub`：在其中部署 Azure Purview 帐户的订阅 ID。

## <a name="verify-your-permissions"></a>验证你的权限

请确保用户具有以下角色和权限：

角色或权限 | 范围 |
|-------|--------|
| **全局读取者** | Azure AD 租户 |
| **读者** | Azure 数据源所在的 Azure 订阅 |
| **读者** | 在其中创建 Azure Purview 帐户的订阅 |
| SQL 管理员（Azure AD 身份验证） | Azure Synapse 专用池、Azure SQL 数据库实例、Azure SQL 托管实例 |
| 访问 Azure 密钥保管库 | 访问 get/list 密钥保管库的机密或 Azure 密钥保管库机密用户 |  


## <a name="run-the-client-side-readiness-script"></a>运行客户端就绪情况脚本

通过完成以下步骤来运行该脚本：

1. 使用以下命令前往该脚本的文件夹。 将 `<path-to-script>` 替换为提取文件的文件夹路径。

   ```powershell
   cd <path-to-script>
   ```

2. 执行以下命令以设置本地计算机的执行策略。 当系统提示你更改执行策略时，输入“A”表示“全部更改”。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

3. 使用以下参数运行脚本。 替换 `DataType`、`PurviewName` 和 `SubscriptionID` 占位符。

   ```powershell
   .\purview-data-sources-readiness-checklist.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   运行该命令时，页面上可能会出现一个弹出窗口，提示你使用 Azure Active Directory 凭据登录到 Azure 和 Azure AD。


创建报表可能需要几分钟，具体取决于环境中 Azure 订阅和资源的数量。 

完成此过程后，请查看输出报告，其中演示了 Azure 订阅或资源中检测到的缺失配置。 结果可能显示为“已通过”、“未通过”或“感知”。 你可以与组织中的相应订阅管理员共享结果，以便他们可以配置所需设置。


## <a name="more-information"></a>详细信息

### <a name="what-data-sources-are-supported-by-the-script"></a>脚本支持哪些数据源？

脚本目前支持以下数据源：

- Azure Blob 存储 (BlobStorage)
- Azure Data Lake Storage Gen2 (ADLSGen2)
- Azure Data Lake Storage Gen1 (ADLSGen1)
- Azure SQL 数据库 (AzureSQLDB)
- Azure SQL 托管实例 (AzureSQLMI)
- Azure Synapse (Synapse) 专用池

运行脚本时，可以选择所有这些数据源或任一数据源作为输入参数。

### <a name="what-checks-are-included-in-the-results"></a>结果中包含哪些检查？

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob 存储 (BlobStorage)

- RBAC。 检查 Azure Purview MSI 是否在所选范围内的每个订阅中分配了“存储 Blob 数据读取者”角色。
- RBAC。 检查 Azure Purview MSI 是否在所选范围内分配了“读取”角色。
- 服务终结点。 请检查服务终结点是否打开，并检查是否启用“允许受信任的 Microsoft 服务访问此存储帐户”。
- 网络：检查是否为存储创建了专用终结点，并且为 Blob 存储启用专用终结点。

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC。 检查 Azure Purview MSI 是否在所选范围内的每个订阅中分配了“存储 Blob 数据读取者”角色。
- RBAC。 检查 Azure Purview MSI 是否在所选范围内分配了“读取”角色。
- 服务终结点。 请检查服务终结点是否打开，并检查是否启用“允许受信任的 Microsoft 服务访问此存储帐户”。
- 网络：检查是否为存储创建了专用终结点，并且为 Blob 存储启用专用终结点。

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- 联网。 请检查服务终结点是否打开，并检查是否启用“允许所有 Azure 服务访问此 Data Lake Storage Gen1 帐户”。
- 权限。 检查 Azure Purview MSI 是否具有读取/执行权限。

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL 数据库 (AzureSQLDB)

- SQL Server 实例：
  - Network. 检查是否启用了公共终结点或专用终结点。
  - 防火墙。 检查是否启用“允许 Azure 服务和资源访问此服务器”。
  - Azure AD 管理。 检查 Azure SQL Server 是否具有 Azure AD 身份验证。
  - Azure AD 管理。 向 Azure SQL Server 填充 Azure AD 管理员用户或组。

- SQL 数据库：
  - SQL 角色。 检查 Azure Purview MSI 是否分配了“db_datareader”角色。

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL 托管实例 (AzureSQLMI)

- SQL 托管实例服务器：
  - Network. 检查是否启用了公共终结点或专用终结点。
  - ProxyOverride。 检查 Azure SQL 托管实例是否已配置为“代理”或“重定向”。
  - 联网。 检查 NSG 是否具有入站规则以允许 AzureCloud 通过所需端口：  
    - 重定向：1433 和 11000-11999  
    或
    - 代理：3342
  - Azure AD 管理。 检查 Azure SQL Server 是否具有 Azure AD 身份验证。
  - Azure AD 管理。 向 Azure SQL Server 填充 Azure AD 管理员用户或组。

- SQL 数据库：
  - SQL 角色。 检查 Azure Purview MSI 是否分配了“db_datareader”角色。

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Azure Synapse (Synapse) 专用池

- RBAC。 检查 Azure Purview MSI 是否在所选范围内的每个订阅中分配了“存储 Blob 数据读取者”角色。
- RBAC。 检查 Azure Purview MSI 是否在所选范围内分配了“读取”角色。
- SQL Server 实例（专用池）：
  - 网络：检查是否启用了公共终结点或专用终结点。
  - 防火墙：检查是否启用“允许 Azure 服务和资源访问此服务器”。
  - Azure AD 管理：检查 Azure SQL Server 是否具有 Azure AD 身份验证。
  - Azure AD 管理：向 Azure SQL Server 填充 Azure AD 管理员用户或组。

- SQL 数据库：
  - SQL 角色。 检查 Azure Purview MSI 是否分配了“db_datareader”角色。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：
> [!div class="checklist"]
>
> * 在 Azure Purview 中注册和扫描 Azure 订阅之前，大规模运行 Azure Purview 就绪情况清单以检查 Azure 订阅是否缺少配置。

转到下一教程了解如何确定所需的访问权限，并为 Azure Purview 跨 Azure 数据源设置所需的身份验证和网络规则：

> [!div class="nextstepaction"]
> [大规模配置对 Azure Purview MSI 数据源的访问权限](tutorial-msi-configuration.md)
