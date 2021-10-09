---
title: 为 Azure-SSIS Integration Runtime 启用 Azure Active Directory 身份验证
description: 本文介绍如何使用 Azure 数据工厂的指定系统/用户分配的托管标识启用 Azure Active Directory 身份验证，以创建 Azure-SSIS Integration Runtime。
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/27/2021
ms.openlocfilehash: f887d3370364f899a1e7afd527c9a146b20bd637
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081389"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>为 Azure-SSIS Integration Runtime 启用 Azure Active Directory 身份验证

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何使用 Azure 数据工厂 (ADF) 的指定系统/用户分配的托管标识启用 Azure Active Directory (Azure AD) 身份验证，并使用该身份验证代替常规身份验证方法（如 SQL 身份验证）执行以下操作：

- 创建 Azure-SSIS Integration Runtime (IR)，而该运行时会代表你在 Azure SQL 数据库服务器/托管实例中预配 SSIS 目录数据库 (SSISDB)。

- 在 Azure-SSIS IR 上运行 SSIS 包时连接到各种 Azure 资源。

有关 ADF 的托管标识的详细信息，请参阅[数据工厂的托管标识](./data-factory-service-identity.md)。

> [!NOTE]
> - 在此场景中，只能在预配和后续启动 Azure-SSIS IR 过程中使用 ADF 的指定系统/用户分配的托管标识进行 Azure AD 身份验证，而 Azure-SSIS IR 可以预配并连接到 SSISDB。 在 SSIS 包的执行过程中，Azure-SSIS IR 仍通过使用在 SSISDB 预配期间创建的完全托管帐户（AzureIntegrationServiceDbo 和 AzureIntegrationServiceWorker）进行 SQL 身份验证来连接到 SSISDB 以提取包。 
>
> - 如果已使用 SQL 身份验证创建了 Azure SSIS IR，则此时不能通过 PowerShell 将其重新配置为使用 Azure AD 身份验证，但你可以通过 Azure 门户/ADF 应用执行此操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-authentication-on-azure-sql-database"></a>在 Azure SQL 数据库上启用 Azure AD 身份验证

Azure SQL 数据库支持使用 Azure AD 用户创建数据库。 首先，需要创建一个 Azure AD 组，并将 ADF 的指定系统/用户分配的托管标识作为成员。 接下来，需要将 Azure AD 用户设置为 Azure SQL 数据库服务器的 Active Directory 管理员，然后使用该用户在 SQL Server Management Studio (SSMS) 上连接到该服务器。 最后，需要创建一个代表 Azure AD 组的内含用户，以便 Azure-SSIS IR 可以使用 ADF 的指定系统/用户分配的托管标识代表你创建 SSISDB。

### <a name="create-an-azure-ad-group-with-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-member"></a>创建一个 Azure AD 组，并将 ADF 的指定系统/用户分配的托管标识作为成员

可以使用现有的 Azure AD 组，或使用 Azure AD PowerShell 创建新组。

1. 安装 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) 模块。

2. 使用 `Connect-AzureAD` 登录，运行以下 cmdlet 来创建组，并将该组保存在变量中：

   ```powershell
   $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                             -MailEnabled $false `
                             -SecurityEnabled $true `
                             -MailNickName "NotSet"
   ```

   结果如以下示例所示，此示例还显示变量值：

   ```powershell
   $Group

   ObjectId DisplayName Description
   -------- ----------- -----------
   6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
   ```

3. 将 ADF 的指定系统/用户分配的托管标识添加到组中。 可以按照[数据工厂的托管标识](./data-factory-service-identity.md)一文获取 ADF 的指定系统/用户分配的托管标识的对象 ID（例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc，但不要将应用程序 ID 用于此目的）。

   ```powershell
   Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
   ```

   之后还可以检查组成员身份。

   ```powershell
   Get-AzureAdGroupMember -ObjectId $Group.ObjectId
   ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database"></a>为 Azure SQL 数据库配置 Azure AD 身份验证

可以使用以下步骤[为 Azure SQL 数据库配置和管理 Azure AD 身份验证](../azure-sql/database/authentication-aad-configure.md)：

1. 在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”   。

2. 选择要使用 Azure AD 身份验证配置的 Azure SQL 数据库服务器。

3. 在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。  

4. 在命令栏中，选择“设置管理员”。

5. 选择要设为服务器管理员的 Azure AD 用户帐户，然后选择“选择”。

6. 在命令栏中，选择“保存”。

### <a name="create-a-contained-user-in-azure-sql-database-representing-the-azure-ad-group"></a>在 Azure SQL 数据库中创建代表 Azure AD 组的内含用户

有关此后续步骤，需要使用 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)。

1. 启动 SSMS。

2. 在“连接到服务器”对话框的“服务器名称”字段中，输入服务器名称 。

3. 在“身份验证”字段中，选择“Active Directory - 支持 MFA 的通用方法”（还可以使用其他两种 Active Directory 身份验证类型，请参阅[为 Azure SQL 数据库配置和管理 Azure AD 身份验证](../azure-sql/database/authentication-aad-configure.md) ）。

4. 在“用户名”字段中，输入已设为服务器管理员的 Azure AD 帐户的名称，例如 testuser@xxxonline.com。

5. 选择“连接”并完成登录过程。

6. 在“对象资源管理器”中，展开“数据库” -> “系统数据库”文件夹  。

7. 右键单击 master 数据库并选择“新建查询” 。

8. 在查询窗口中，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令应会成功完成，并创建内含用户来表示组。

9. 清除查询窗口，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令应会成功完成，并授予该内含用户创建数据库的权限 (SSISDB)。

10. 如果 SSISDB 是使用 SQL 身份验证创建的，并且你想要改为对 Azure-SSIS IR 使用 Azure AD 身份验证来访问它，请首先确保上述向主数据库授予权限的步骤已成功完成。 然后，右键单击 SSISDB 数据库并选择“新建查询” 。

    1. 在查询窗口中，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

       ```sql
       CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
       ```

       命令应会成功完成，并创建内含用户来表示组。

    2. 清除查询窗口，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

       ```sql
       ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
       ```

       命令应会成功完成，并授予该内含用户访问 SSISDB 的权限。

## <a name="enable-azure-ad-authentication-on-azure-sql-managed-instance"></a>在 Azure SQL 托管实例上启用 Azure AD 身份验证

Azure SQL 托管实例支持直接使用 ADF 的指定系统/用户分配的托管标识创建数据库。 无需将 ADF 的指定系统/用户分配的托管标识加入 Azure AD 组，也无需在 Azure SQL 托管实例中创建代表该组的内含用户。

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>为 Azure SQL 托管实例配置 Azure AD 身份验证

按照[为 Azure SQL 托管实例预配 Azure AD 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)中的步骤进行操作。

### <a name="add-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-user-in-azure-sql-managed-instance"></a>将 ADF 的指定系统/用户分配的托管标识添加为 Azure SQL 托管实例中的用户

有关此后续步骤，需要使用 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)。

1. 启动 SSMS。

2. 使用 SQL Server 帐户（即 sysadmin）连接到 Azure SQL 托管实例。 这是一个临时限制，在 Azure SQL 托管实例上的 Azure AD 服务器主体（登录名）支持正式推出后就会解除。 如果尝试使用 Azure AD 管理员帐户创建登录名，将会看到以下错误：消息 15247，级别 16，状态 1，行 1 用户无权执行此操作。

3. 在“对象资源管理器”中，展开“数据库” -> “系统数据库”文件夹  。

4. 右键单击 master 数据库并选择“新建查询” 。

5. 在查询窗口中，执行以下 T-SQL 脚本，将 ADF 的指定系统/用户分配的托管标识添加为用户。

   ```sql
   CREATE LOGIN [{your managed identity name}] FROM EXTERNAL PROVIDER
   ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your managed identity name}]
   ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your managed identity name}]
   ```

   如果将系统托管标识用于 ADF，则托管标识名称应为 ADF 名称。 如果将用户分配的托管标识用于 ADF，则托管标识名称应为指定的用户分配的托管标识名称。
    
   命令应会成功完成，并授予 ADF 的指定系统/用户分配的托管标识创建数据库 (SSISDB) 的权限。

6. 如果 SSISDB 是使用 SQL 身份验证创建的，并且你想要改为对 Azure-SSIS IR 使用 Azure AD 身份验证来访问它，请首先确保上述向主数据库授予权限的步骤已成功完成。 然后，右键单击 SSISDB 数据库并选择“新建查询” 。

   1. 在查询窗口中，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

      ```sql
      CREATE USER [{your managed identity name}] FOR LOGIN [{your managed identity name}] WITH DEFAULT_SCHEMA = dbo
      ALTER ROLE db_owner ADD MEMBER [{your managed identity name}]
      ```

      该命令应会成功完成，并授予 ADF 的指定系统/用户分配的托管标识访问 SSISDB 的权限。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>在 Azure 门户/ADF 应用中预配 Azure-SSIS IR

在 Azure 门户/ADF 应用中预配 Azure-SSIS IR 时，在“部署设置”页上，选择“创建由 Azure SQL 数据库服务器/托管实例承载的 SSIS 目录(SSISDB)来存储项目/包/环境/执行日志”复选框，然后选中“使用数据工厂的系统托管标识进行 AAD 身份验证”或“使用数据工厂的用户分配的托管标识进行 AAD 身份验证”复选框，为 Azure-SSIS IR 选择使用 Azure AD 身份验证方法来访问承载 SSISDB 的数据库服务器   。

有关详细信息，请参阅[在 ADF 中创建 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)。

## <a name="provision-azure-ssis-ir-with-powershell"></a>通过 PowerShell 预配 Azure-SSIS IR

若要通过 PowerShell 预配 Azure-SSIS IR，请执行以下操作：

1. 安装 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 模块。

2. 在脚本中，不要设置 `CatalogAdminCredential` 参数。 例如：

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                         -DataFactoryName $DataFactoryName `
                                         -Name $AzureSSISName `
                                         -Description $AzureSSISDescription `
                                         -Type Managed `
                                         -Location $AzureSSISLocation `
                                         -NodeSize $AzureSSISNodeSize `
                                         -NodeCount $AzureSSISNodeNumber `
                                         -Edition $AzureSSISEdition `
                                         -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                         -CatalogServerEndpoint $SSISDBServerEndpoint `
                                         -CatalogPricingTier $SSISDBPricingTier

   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName
   ```

## <a name="run-ssis-packages-using-azure-ad-authentication-with-the-specified-systemuser-assigned-managed-identity-for-your-adf"></a>使用具有 ADF 的指定系统/用户分配的托管标识的 Azure AD 身份验证运行 SSIS 包

在 Azure-SSIS IR 上运行 SSIS 包时，可以使用具有 ADF 的指定系统/用户分配的托管标识的 Azure AD 身份验证，以连接到各种 Azure 资源。 当前，支持在以下连接管理器上使用 ADF 的指定系统/用户分配的托管标识进行 Azure AD 身份验证。

- [OLEDB 连接管理器](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 连接管理器](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure 存储连接管理器](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
