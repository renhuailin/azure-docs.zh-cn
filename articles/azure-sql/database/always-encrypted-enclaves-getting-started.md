---
title: 教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted
description: 本教程介绍如何为 Azure SQL 数据库中具有安全 Enclave 的 Always Encrypted 创建基本环境，如何就地加密数据，以及如何使用 SQL Server Management Studio (SSMS) 对加密列发出各种机密查询。
keywords: 加密数据, sql 加密, 数据库加密, 敏感数据, 始终加密, 安全 enclave, SGX, 证明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252135"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>教程：在 Azure SQL 数据库中开始使用具有安全 enclave 的 Always Encrypted

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库的具有安全 Enclave 的 Always Encrypted 目前提供公共预览版。

本教程介绍如何在 Azure SQL 数据库中开始使用[具有安全 enclave 的 Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves)。 它将介绍：

> [!div class="checklist"]
> - 如何创建环境，以便测试和评估具有安全 enclave 的 Always Encrypted。
> - 如何使用 SQL Server Management Studio (SSMS) 就地加密数据并就加密列发出各种机密查询。

## <a name="prerequisites"></a>必备条件

本教程需要 Azure PowerShell 和 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

### <a name="powershell-requirements"></a>PowerShell 要求

要了解如何安装和运行 Azure PowerShell，请参阅 [Azure PowerShell 概述](https://docs.microsoft.com/powershell/azure)。 

支持证明操作所需的 Az 模块的最低版本：

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

运行以下命令，验证所有 Az 模块的已安装版本：

```powershell
Get-InstalledModule
```

如果版本与最低要求不匹配，请运行 `Update-Module` 命令。

PowerShell 库已弃用传输层安全性 (TLS) 版本 1.0 和 1.1。 建议使用 TLS 1.2 或更高版本。 如果使用的 TLS 版本低于 1.2，则可能会收到以下错误：

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

若要继续与 PowerShell 库进行交互，请在 Install-Module 命令之前运行以下命令

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>SSMS 要求

请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 以获取有关如何下载 SSMS 的信息。

SSMS 的最低版本要求为 18.8。


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>步骤 1：创建服务器和 DC 系列数据库

 此步骤将使用 DC 系列硬件配置创建新的 Azure SQL 数据库逻辑服务器和新数据库。 Azure SQL 数据库中具有安全 Enclave 的 Always Encrypted 使用 Intel SGX enclave，这在 DC 系列硬件配置中受支持。 有关更多信息，请参见 [DC 系列](service-tiers-vcore.md#dc-series)。

1. 打开 PowerShell 控制台并登录到 Azure。 如果需要，[切换到本教程中使用的订阅](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps)。

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. 创建包含数据库服务器的资源组。 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > 需要在支持 DC 系列硬件配置的区域创建资源组。 有关当前支持的区域列表，请参阅 [DC 系列可用性](service-tiers-vcore.md#dc-series-1)。

3. 创建数据库服务器。 出现提示时，请输入服务器管理员名称和密码。

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. 创建允许从指定 IP 范围访问的服务器防火墙规则
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. 为服务器分配托管系统标识。 稍后将需要它来授予服务器对 Microsoft Azure 证明的访问权限。

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. 检索分配给服务器的标识的对象 ID。 保存生成的对象 ID。 稍后部分将需要此 ID。

  > [!NOTE]
  > 新分配的托管系统标识可能需要几秒钟才能在 Azure Active Directory 中传播。 如果下面的脚本返回空结果，请重试。

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. 创建 DC 系列数据库。

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>步骤 2：配置证明提供程序

此步骤将在 Microsoft Azure 证明中创建和配置证明提供程序。 这是证明数据库服务器中的安全 enclave 所必需的。

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

2. 导入所需的 `Az.Accounts` 和 `Az.Attestation` 版本。  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. 为证明提供程序创建资源组。

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. 创建证明提供程序。 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. 配置证明策略。
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. 授予 Azure SQL 逻辑服务器对证明提供程序的访问权限。 在此步骤中，我们将使用先前分配给服务器的托管服务标识的对象 ID。

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. 检索证明 URL。

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  保存指向为 SGX enclave 配置的证明策略的结果证明 URL。 稍后需要用到此信息。 证明 URL 应如下所示：`https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>步骤 3：填充数据库

此步骤将创建一个表，并使用一些稍后将加密和查询的数据填充该表。

1. 打开 SSMS 并连接到 Azure SQL 逻辑服务器（在数据库连接中未启用 Always Encrypted 的情况下创建）中的 ContosoHR 数据库 。
    1. 在“连接到服务器”对话框中，指定服务器名称（例如 myserver123.database.windows.net），并输入先前配置的用户名和密码。
    2. 单击“选项 >>”，然后选择“连接属性”选项卡 。确保选择“ContosoHR”数据库（而不是默认的主数据库）。 
    3. 选择“Always Encrypted”选项卡。
    4. 务必取消选中“启用 Always Encrypted (列加密)”复选框。

        ![没有 Always Encrypted 的连接](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

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

        ![允许 enclave 计算](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. 创建已启用 enclave 的新列加密密钥：

    1. 右键单击“Always Encrypted 密钥”，然后选择“新建列加密密钥” 。
    2. 输入新列加密密钥的名称：CEK1。
    3. 在“列主密匙”下拉列表中，选择在上一步骤创建的列主密钥。
    4. 选择“确定”。

## <a name="step-5-encrypt-some-columns-in-place"></a>步骤 5：就地加密部分列

这一步将在服务器端 enclave 内加密“SSN”和“Salary”列中存储的数据，然后对数据测试 SELECT 查询 。

1. 打开新的 SSMS 实例并连接到数据库，同时为数据库连接启用 Always Encrypted。
    1. 启动新 SSMS 实例。
    2. 在“连接到服务器”对话框中，指定服务器名称，选择身份验证方法，并指定凭据。
    3. 单击“选项 >>”，然后选择“连接属性”选项卡 。确保选择“ContosoHR”数据库（而不是默认的主数据库）。 
    4. 选择“Always Encrypted”选项卡。
    5. 务必选中“启用 Always Encrypted (列加密)”复选框。
    6. 指定你的 enclave 证明 URL，你可以通过遵循[步骤 2：配置证明提供程序](#step-2-configure-an-attestation-provider)中的步骤获得。 请参阅以下屏幕截图。

        ![与证明连接](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

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
- [教程：使用具有安全 enclave 的 Always Encrypted 开发 .NET 应用程序](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [教程：开发使用具有安全 enclave 的 Always Encrypted 的 .NET Framework 应用程序](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [教程：对使用随机加密的启用了 enclave 的列创建和使用索引](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>另请参阅

- [配置和使用具有安全 enclave 的 Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)