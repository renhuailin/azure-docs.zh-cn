---
title: 教程：开始使用具有安全 enclave 的 Always Encrypted
description: 本教程介绍如何为 Azure SQL 数据库中具有安全 Enclave 的 Always Encrypted 创建基本环境，如何就地加密数据，以及如何使用 SQL Server Management Studio (SSMS) 对加密列发出各种机密查询。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: dd8fc18b8f24a6164830dda6044c1b03151eb180
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727333"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本教程介绍如何在 Azure SQL 数据库中开始使用[具有安全 enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)。 它将介绍：

> [!div class="checklist"]
> - 如何创建环境，以便测试和评估具有安全 enclave 的 Always Encrypted。
> - 如何使用 SQL Server Management Studio (SSMS) 就地加密数据并就加密列发出各种机密查询。

## <a name="prerequisites"></a>先决条件

- 一个有效的 Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。 你需要是订阅的参与者角色或所有者角色的成员，才能创建资源和配置证明策略。

- SQL Server Management Studio (SSMS) 版本 18.9.1 或更高版本。 请参阅[下载 SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) 以获取有关如何下载 SSMS 的信息。

### <a name="powershell-requirements"></a>PowerShell 要求

> [!NOTE]
> 仅当你选择使用 PowerShell 来完成本教程中的某些步骤时，本部分列出的先决条件才适用。 如果你打算改用 Azure 门户，则可以跳过本部分。

确保已在计算机上安装以下 PowerShell 模块。

1. Az 版本 5.6 或更高版本。 有关如何安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。 若要确定计算机上安装的 Az 模块版本，请在 PowerShell 会话中运行以下命令。

    ```powershell
    Get-InstalledModule -Name Az
    ```

1. Az.Attestation 0.1.8 或更高版本。 有关如何安装 Az.Attestation PowerShell 模块的详细信息，请参阅[安装 Az.Attestation PowerShell 模块](../../attestation/quickstart-powershell.md#install-azattestation-powershell-module)。 若要确定计算机上安装的 Az.Attestation 模块版本，请在 PowerShell 会话中运行以下命令。

    ```powershell
    Get-InstalledModule -Name Az.Attestation
    ```

如果版本不符合最低要求，请运行 `Update-Module` 命令。

PowerShell 库已弃用传输层安全性 (TLS) 版本 1.0 和 1.1。 建议使用 TLS 1.2 或更高版本。 如果使用的 TLS 版本低于 1.2，则可能会收到以下错误：

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

若要继续与 PowerShell 库进行交互，请在 Install-Module 命令之前运行以下命令

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>步骤 1：创建并配置服务器和 DC 系列数据库

此步骤将使用 DC 系列硬件代系创建新的 Azure SQL 数据库逻辑服务器和数据库，这对于使用安全领域的 Always Encrypted 而言是必需的操作。 有关详细信息，请参阅 [DC 系列](service-tiers-sql-database-vcore.md#dc-series)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 浏览到“[选择 SQL 部署选项](https://portal.azure.com/#create/Microsoft.AzureSQL)”页。
1. 如果你尚未登录到 Azure 门户，请按提示登录。
1. 在“SQL 数据库”下将“资源类型”设置保留为“单一数据库”，然后选择“创建”   。

   :::image type="content" source="./media/single-database-create-quickstart/select-deployment.png" alt-text="添加到 Azure SQL":::

1. 在“创建 SQL 数据库”窗体的“基本信息”选项卡上的“项目详细信息”下，选择所需的 Azure订阅   。
1. 对于“资源组”，请选择“新建”，输入资源组的名称，然后选择“确定”  。
1. 对于“数据库名称”，请输入 ContosoHR。
1. 对于“服务器”，选择“新建”，并使用以下值填写“新服务器”窗体  ：
   - **服务器名称**：输入“mysqlserver”并添加一些字符以实现唯一性。 我们无法提供要使用的确切服务器名称，因为对于 Azure 中的所有服务器，服务器名称必须全局唯一，而不只是在订阅中唯一。 因此请输入类似于 mysqlserver135 的名称，然后门户会告知你该名称是否可用。
   - 服务器管理员登录名：输入管理员登录名，例如 azureuser。
   - **密码**：输入符合要求的密码，然后在“确认密码”字段中再次输入该密码。
   - 位置：从下拉列表中选择一个位置。
      > [!IMPORTANT]
      > 需要选择一个既支持 DC 系列硬件代系，也支持 Microsoft Azure 证明的位置（Azure 区域）。 有关支持 DC 系列的区域列表，请参阅 [DC 系列可用性](service-tiers-sql-database-vcore.md#dc-series)。 [此文档](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation)介绍了 Microsoft Azure 证明的区域可用性。

   选择“确定”。
1. 将“想要使用 SQL 弹性池”设置保留为“否” 。
1. 在“计算 + 存储”下，选择“配置数据库”，然后单击“更改配置”  。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database.png" alt-text="配置数据库" lightbox="./media/always-encrypted-enclaves/portal-configure-database.png":::

1. 选择“DC 系列”硬件配置，然后选择“确定” 。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database.png" alt-text="配置 DC 系列数据库":::

1. 选择“应用”。 
1. 返回“基本信息”选项卡，确认“计算 + 存储”是否设置为“常规用途”和“DC，2 vCore，32 GB 存储”   。
1. 在完成时选择“下一步:网络”。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-dc-series-database-basics.png" alt-text="配置 DC 系列数据库 - 基本信息":::

1. 在“网络”选项卡上，对于“连接方法”，选择“公共终结点”  。
1. 对于“防火墙规则”，将“添加当前客户端 IP 地址”设置为“是”  。 将“允许 Azure 服务和资源访问此服务器”设置保留为“否” 。
1. 在页面底部选择“查看 + 创建”。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-database-networking.png" alt-text="新建 SQL 数据库 - 网络":::

1. 在“查看 + 创建”页上，查看后选择“创建”。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 打开 PowerShell 控制台并导入所需的 Az 版本。

   ```PowerShell
   Import-Module "Az" -MinimumVersion "5.6.0"
   ```

1. 登录到 Azure。 如果需要，[切换到本教程中使用的订阅](/powershell/azure/manage-subscriptions-azureps)。

   ```PowerShell
   Connect-AzAccount
   $subscriptionId = "<your subscription ID>"
   Set-AzContext -Subscription $subscriptionId
   ```

1. 创建新的资源组。

   > [!IMPORTANT]
   > 需要在既支持 DC 系列硬件代系，也支持 Microsoft Azure 证明的区域（位置）创建资源组。 有关支持 DC 系列的区域列表，请参阅 [DC 系列可用性](service-tiers-sql-database-vcore.md#dc-series)。 [此文档](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation)介绍了 Microsoft Azure 证明的区域可用性。

   ```powershell
   $resourceGroupName = "<your new resource group name>"
   $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
   New-AzResourceGroup -Name $resourceGroupName -Location $location
   ```

1. 创建 Azure SQL 逻辑服务器。 出现提示时，请输入服务器管理员名称和密码。 请务必记住管理员名称和密码 - 稍后需使用它们连接到服务器。 

   ```powershell
   $serverName = "<your server name>" 
   New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
   ```

1. 创建允许从指定 IP 范围进行访问的服务器防火墙规则。
  
   ```powershell
   $startIp = "<start of IP range>"
   $endIp = "<end of IP range>"
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   ```

1. 创建 DC 系列数据库。

   ```powershell
   $databaseName = "ContosoHR"
   $edition = "GeneralPurpose"
   $vCore = 2
   $generation = "DC"
   New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
   ```

---

## <a name="step-2-configure-an-attestation-provider"></a>步骤 2：配置证明提供程序

此步骤将在 Microsoft Azure 证明中创建和配置证明提供程序。 需要执行此步骤才能证明数据库使用的安全领域。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 浏览到“[创建证明提供程序](https://ms.portal.azure.com/#create/Microsoft.Attestation)”页。
1. 在“创建证明提供程序”页面上，提供以下输入：

   - 订阅：选择在其中创建了 Azure SQL 逻辑服务器的同一订阅。
   - 资源组：选择在其中创建了 Azure SQL 逻辑服务器的同一资源组。
   - 名称：输入 myattestprovider，并添加一些字符以确保名称唯一。 我们无法提供要使用的确切证明提供程序名称，因为名称必须全局唯一。 因此请输入类似于 myattestprovider12345 的名称，然后门户会告知你该名称是否可用。
   - 位置：选择在其中创建了 Azure SQL 逻辑服务器的位置。
   - 策略签名者证书文件：将此字段留空，因为你将配置未签名的策略。

1. 提供所需的输入后，选择“查看 + 创建”。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-create-attestation-provider-basics.png" alt-text="创建证明提供程序":::

1. 选择“创建”。
1. 创建证明提供程序后，单击“转到资源”。
1. 在证明提供程序的“概述”选项卡上，将“证明 URI”属性的值复制到剪贴板，并将其保存到某个文件中 。 在后续步骤中需要用到此证明 URL。  

   :::image type="content" source="./media/always-encrypted-enclaves/portal-attest-uri.png" alt-text="证明 URL":::

1. 在下窗格中或在窗口左侧的“资源”菜单上选择“策略”。
1. 将“证明类型”设置为“SGX-IntelSDK” 。
1. 在上侧菜单上选择“配置”。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-configure-attestation-policy.png" alt-text="配置证明策略":::

1. 将“策略格式”设置为“文本” 。 将“策略选项”保留设置为“输入策略” 。
1. 在“策略文本”字段中，将默认策略替换为以下策略。 有关以下策略的详细信息，请参阅[创建并配置证明提供程序](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)。

    ```output
    version= 1.0;
    authorizationrules 
    {
           [ type=="x-ms-sgx-is-debuggable", value==false ]
            && [ type=="x-ms-sgx-product-id", value==4639 ]
            && [ type=="x-ms-sgx-svn", value>= 0 ]
            && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
        => permit();
    };
    ```

1. 单击“保存” 。

   :::image type="content" source="./media/always-encrypted-enclaves/portal-edit-attestation-policy.png" alt-text="编辑证明策略":::

1. 在上方菜单中单击“刷新”查看配置的策略。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 复制以下证明策略并将策略保存在文本文件 (txt) 中。 有关以下策略的详细信息，请参阅[创建并配置证明提供程序](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)。

    ```output
    version= 1.0;
    authorizationrules 
    {
           [ type=="x-ms-sgx-is-debuggable", value==false ]
            && [ type=="x-ms-sgx-product-id", value==4639 ]
            && [ type=="x-ms-sgx-svn", value>= 0 ]
            && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
        => permit();
    };
    ```

1. 导入所需的 `Az.Attestation` 版本。  

   ```powershell
   Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
   ```
  
1. 创建证明提供程序。

   ```powershell
   $attestationProviderName = "<your attestation provider name>" 
   New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
   ```

1. 配置证明策略。
  
   ```powershell
   $policyFile = "<the pathname of the file from step 1 in this section>"
   $teeType = "SgxEnclave"
   $policyFormat = "Text"
   $policy=Get-Content -path $policyFile -Raw
   Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
   ```

1. 检索证明 URI（证明提供程序的证明 URL）。

   ```powershell
   $attestationUrl = (Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName).AttestUri
   Write-Host "Your attestation URL is: $attestationUrl"
   ```

   证明 URL 应如下所示：`https://myattestprovider12345.eus.attest.azure.net`

---


## <a name="step-3-populate-your-database"></a>步骤 3：填充数据库

此步骤将创建一个表，并使用一些稍后将加密和查询的数据填充该表。

1. 打开 SSMS 并连接到 Azure SQL 逻辑服务器（在数据库连接中未启用 Always Encrypted 的情况下创建）中的 ContosoHR 数据库 。
    1. 在“连接到服务器”对话框中，指定服务器的完全限定名称（例如 myserver135.database.windows.net），并输入在创建服务器时指定的管理员用户名和密码。
    2. 单击“选项 >>”，然后选择“连接属性”选项卡 。确保选择“ContosoHR”数据库（而不是默认的主数据库）。 
    3. 选择“Always Encrypted”选项卡。
    4. 务必取消选中“启用 Always Encrypted (列加密)”复选框。

        :::image type="content" source="./media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png" alt-text="没有 Always Encrypted 的连接":::

    5. 单击“连接”  。

2. 新建名为“Employees”的表。

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. 向“Employees”表添加一些员工记录。

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```

## <a name="step-4-provision-enclave-enabled-keys"></a>步骤 4：预配已启用 enclave 的密钥

此步骤将创建允许 enclave 计算的列主密钥和列加密密钥。

1. 使用上一步中的 SSMS 实例在“对象资源管理器”中展开数据库，并依次转到“安全性” > “Always Encrypted 密钥”。
1. 预配已启用 enclave 的新列主密钥：
    1. 右键单击“Always Encrypted 密钥”，然后选择“新列主密钥...” 。
    2. 选择列主密钥名称：CMK1。
    3. 请确保选择“Windows 证书存储(当前用户或本地计算机)”或“Azure Key Vault” 。
    4. 选择“允许 enclave 计算”。
    5. 如果选择了 Azure Key Vault，请登录到 Azure 并选择密钥保管库。 若要深入了解如何创建 Always Encrypted 的密钥保管库，请参阅 [Manage your key vaults from Azure portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal)（从 Azure 门户管理密钥保管库）。
    6. 选择证书或 Azure Key Value 密钥（如果已存在），或单击“生成证书”按钮，创建新证书。
    7. 选择“确定”。

        :::image type="content" source="./media/always-encrypted-enclaves/allow-enclave-computations.png" alt-text="允许 enclave 计算":::

1. 创建已启用 enclave 的新列加密密钥：

    1. 右键单击“Always Encrypted 密钥”，然后选择“新建列加密密钥” 。
    2. 输入新列加密密钥的名称：CEK1。
    3. 在“列主密匙”下拉列表中，选择在上一步骤创建的列主密钥。
    4. 选择“确定”。

## <a name="step-5-encrypt-some-columns-in-place"></a>步骤 5：就地加密部分列

这一步将在服务器端 enclave 内加密“SSN”和“Salary”列中存储的数据，然后对数据测试 SELECT 查询 。

1. 打开新的 SSMS 实例并连接到数据库，同时为数据库连接启用 Always Encrypted。
    1. 启动新 SSMS 实例。
    2. 在“连接到服务器”对话框中，指定服务器的完全限定名称（例如 myserver135.database.windows.net），并输入在创建服务器时指定的管理员用户名和密码。
    3. 单击“选项 >>”，然后选择“连接属性”选项卡 。确保选择“ContosoHR”数据库（而不是默认的主数据库）。 
    4. 选择“Always Encrypted”选项卡。
    5. 确保已选中“启用 Always Encrypted (列加密)”复选框 。
    6. 指定你的 enclave 证明 URL，你可以通过遵循[步骤 2：配置证明提供程序](#step-2-configure-an-attestation-provider)中的步骤获得。 请参阅以下屏幕截图。

        :::image type="content" source="./media/always-encrypted-enclaves/connect-to-server-configure-attestation.png" alt-text="与证明连接":::

    7. 选择“连接”。
    8. 如果系统提示启用 Always Encrypted 参数化查询，请选择“启用”。

1. 使用相同的 SSMS 实例（已启用 Always Encrypted）打开新查询窗口，并通过运行以下语句来加密“SSN”和“Salary”列 。

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > 请注意上面的脚本中用于为数据库清除查询计划缓存的 ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE 语句。 更改表后，需要清除访问该表的所有批处理和存储过程的计划，以刷新参数加密信息。 

1. 若要验证“SSN”和“Salary”列现在是否已加密，请使用没有为数据库连接启用 Always Encrypted 的 SSMS 实例打开新查询窗口，并执行以下语句。 查询窗口应返回“SSN”和“Salary”列中的加密值。 如果使用已启用 Always Encrypted 的 SSMS 实例执行相同查询，应该会看到已解密的数据。

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>步骤 6：对加密列运行丰富查询

你可以对加密列运行丰富查询。 部分查询处理将在服务器端 enclave 内执行。 

1. 在已启用 Always Encrypted 的SSMS 实例中，请确保也启用了 Always Encrypted 参数化。
    1. 在 SSMS 的主菜单中，选择“工具”。
    2. 选择“选项...”。
    3. 导航到“查询执行” > “SQL Server” > “高级”。
    4. 务必选中“启用 Always Encrypted 参数化”。
    5. 选择“确定”。
2. 打开一个新的查询窗口，粘贴到下面的查询中，然后执行。 该查询应返回满足指定搜索条件的纯文本值和行。

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. 在未启用 Always Encrypted 的 SSMS 实例中重试同一查询。 应该会发生故障。

## <a name="next-steps"></a>后续步骤

完成本教程之后，可以继续学习以下教程之一：
- [教程：使用具有安全 enclave 的 Always Encrypted 开发 .NET 应用程序](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [教程：开发使用具有安全 enclave 的 Always Encrypted 的 .NET Framework 应用程序](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [教程：对使用随机加密的启用了 enclave 的列创建和使用索引](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>另请参阅

- [配置和使用具有安全 enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
