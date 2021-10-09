---
title: 大规模配置对 Azure Purview MSI 数据源的访问权限
description: 在本教程中，你将按照 Azure 数据源订阅配置 Azure MSI 设置。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 101d18cdecdc4fc7d4fb33b824500350f53b7b3f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213387"
---
# <a name="tutorial-configure-access-to-data-sources-for-azure-purview-msi-at-scale"></a>教程：大规模配置对 Azure Purview MSI 数据源的访问权限

若要扫描数据源，Azure Purview 需要访问这些数据源。 本教程适用于 Azure 订阅所有者和 Azure Purview 数据源管理员。 有助你确定所需的访问权限，并为 Azure Purview 跨 Azure 数据源设置所需的身份验证和网络规则。

在本教程系列的第 2 部分中，你将执行以下操作：

> [!div class="checklist"]
>
> * 找到数据源，并准备一个数据源订阅列表。
> * 运行脚本，在 Azure 中跨数据源配置任何缺失的基于角色的访问控制 (RBAC) 或所需的网络配置。
> * 审阅输出报告

## <a name="prerequisites"></a>必备条件

* 数据源所在的 Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure Purview 帐户](create-catalog-portal.md)。
* 每个订阅中的 Azure 密钥保管库资源，其中包含 Azure SQL 数据库、Azure Synapse Analytics 或 Azure SQL 托管实例等数据源。
* [Azure Purview MSI 配置](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration)脚本。

> [!NOTE]
> Azure Purview MSI 配置脚本仅适用于 Windows。
> Azure Purview 托管标识 (MSI) 目前支持此脚本。

> [!IMPORTANT]
> 我们强烈建议你先测试并验证脚本在 Azure 环境中所执行的所有更改，然后再将其部署到生产环境中。

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

1. [将 Azure Purview MSI 配置脚本下载](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration)到所选的位置。

2. 在计算机上，在 Windows 任务栏的搜索框中输入 PowerShell。 在搜索列表中，选择并按住（或右键单击）Windows PowerShell，然后选择“以管理员身份运行” 。

3. 在 PowerShell 窗口中输入以下命令。 （将 `<path-to-script>` 替换为提取的脚本文件的文件夹路径。）

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. 输入以下命令来安装 Azure cmdlet：

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
5. 如果看到提示“需要 NuGet 提供程序才能继续”，请输入“Y”，然后按“Enter”。

6. 如果看到提示“不受信任的存储库”，请输入“A”，然后按“Enter”。

7. 重复前面的步骤以安装 `Az.Synapse` 和 `AzureAD` 模块。

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

至少需要有以下权限才能在 Azure 环境中运行脚本：

角色 | 范围 | 必要性 |
|-------|--------|--------|
| **全局读取者** | Azure AD 租户 | 用来读取 Azure SQL 管理员用户组成员身份和 Azure Purview MSI |
| **全局管理员** | Azure AD 租户 | 用于将“目录读取器”角色分配给 Azure SQL 托管实例 |
| **参与者** | 创建 Azure Purview 帐户时所在的订阅或资源组 | 用于读取 Azure Purview 帐户资源并创建 Key Vault 资源和机密 |
| **所有者或用户管访问理员** | 查找 Azure 数据源时所在的管理组或订阅 | 用于分配 RBAC |
| **参与者** | 查找 Azure 数据源时所在的管理组或订阅 | 用于设置网络配置 |
| SQL 管理员（Azure AD 身份验证） | Azure SQL Server 实例或 Azure SQL 托管实例 | 用于将“db_datareader”角色分配至 Azure Purview |
| 访问 Azure 密钥保管库 | 访问 Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse 身份验证的获取/罗列密钥保管库机密 |  


## <a name="run-the-client-side-readiness-script"></a>运行客户端就绪脚本

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
   .\purview-msi-configuration.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   运行该命令时，页面上可能会出现一个弹出窗口，提示你使用 Azure Active Directory 凭据登录到 Azure 和 Azure AD。

创建报表可能需要几分钟，具体取决于环境中 Azure 订阅和资源的数量。 

如果密钥保管库中的凭据不匹配，系统可能会提示你登录到 Azure SQL Server 实例。 你可以提供凭据，也可以选择“Enter”跳过特定的服务器。 

在此进程完成后，查看输出报表以审阅更改的内容。 


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

### <a name="what-configurations-are-included-in-the-script"></a>脚本中包含哪些配置？

此脚本可帮助你自动完成以下任务：

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob 存储 (BlobStorage)

- RBAC。 将 Azure RBAC“读取者”角色分配给所选范围内的 Azure Purview MSI。 验证分配。 
- RBAC。 将 Azure RBAC“存储 Blob 数据读取者”角色分配给所选范围内每个订阅中的 Azure Purview MSI。 验证分配。
- 联网。 报告是否为存储创建了专用终结点，并且为 Blob 存储启用专用终结点。
- 服务终结点。 如果专用终结点已关闭，请检查服务终结点是否打开，并且启用“允许受信任的 Microsoft 服务访问此存储帐户”。

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC。 将 Azure RBAC“读取者”角色分配给所选范围内的 Azure Purview MSI。 验证分配。 
- RBAC。 将 Azure RBAC“存储 Blob 数据读取者”角色分配给所选范围内每个订阅中的 Azure Purview MSI。 验证分配。
- 联网。 报告是否为存储创建了专用终结点，并且为 Blob 存储启用专用终结点。
- 服务终结点。 如果专用终结点已关闭，请检查服务终结点是否打开，并且启用“允许受信任的 Microsoft 服务访问此存储帐户”。

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- 联网。 验证服务终结点是否打开，并且启用 Data Lake Storage 上的“允许所有 Azure 服务访问此 Data Lake Storage Gen1 帐户”。
- 权限。 分配 Azure Purview MSI 的读取/执行访问权限。 验证该访问权限。 

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL 数据库 (AzureSQLDB)

- SQL Server 实例：
  - Network. 报告是否启用了公共终结点或专用终结点。
  - 防火墙。 如果专用终结点已关闭，请验证防火墙规则并启用“允许 Azure 服务和资源访问此服务器”。
  - Azure AD 管理。 为 Azure SQL 数据库启用 Azure AD 身份验证。

- SQL 数据库：
  - SQL 角色。 将“db_datareader”角色分配给 Azure Purview MSI。

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL 托管实例 (AzureSQLMI)

- SQL 托管实例服务器：
  - Network. 验证公共终结点或专用终结点是否打开。 报告公共终结点是否关闭。
  - ProxyOverride。 验证 Azure SQL 托管实例已配置为“代理”或“重定向”。
  - 联网。 更新 NSG 规则，以允许 AzureCloud 通过规定的端口入站访问 SQL Server 实例：  
    - 重定向：1433 和 11000-11999
    
    或 
    - 代理：3342
    
    验证此访问权限。 
  - Azure AD 管理。 为 Azure SQL 托管实例启用 Azure AD 身份验证。
  
- SQL 数据库：
  - SQL 角色。 将“db_datareader”角色分配给 Azure Purview MSI。

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Azure Synapse (Synapse) 专用池

- RBAC。 将 Azure RBAC“读取者”角色分配给所选范围内的 Azure Purview MSI。 验证分配。 
- RBAC。 将 Azure RBAC“存储 Blob 数据读取者”角色分配给所选范围内每个订阅中的 Azure Purview MSI。 验证分配。
- SQL Server 实例（专用池）：
  - Network. 报告公共终结点或专用终结点是否已打开。
  - 防火墙。 如果专用终结点已关闭，请验证防火墙规则并启用“允许 Azure 服务和资源访问此服务器”。
  - Azure AD 管理。 为 Azure SQL 数据库启用 Azure AD 身份验证。

- SQL 数据库：
  - SQL 角色。 将“db_datareader”角色分配给 Azure Purview MSI。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：
> [!div class="checklist"]
>
> * 如何确定所需的访问权限，并为 Azure Purview Azure 跨 Azure 数据源设置所需的身份验证和网络规则。

转到下一教程，了解如何在 [Azure Purview 中注册和扫描多个源](register-scan-azure-multiple-sources.md)。
