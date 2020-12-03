---
title: '使用 PowerShell 注册和扫描 Power BI (预览版) '
description: 了解如何使用 PowerShell 在 Azure 监控范围中注册和扫描 Power BI 租户。
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551626"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>使用 PowerShell 在 Azure 监控范围 (预览版中注册和扫描 Power BI)  

本文介绍如何使用 PowerShell 在 Azure 监控范围目录中设置对 Power BI 租户的扫描。

## <a name="power-bi-authentication-background"></a>Power BI 身份验证背景

监控范围目录必须连接到 Power BI 管理 API，才能扫描 Power BI 租户中的项目。 Power BI 管理 API 目前支持两种类型的身份验证：

- 托管标识 (MSI) 。
- 委派的用户身份验证。

> [!Note]
> 除非需要委托用户身份验证，否则推荐使用 MSI。

## <a name="create-a-security-group"></a>创建安全组

每个监控范围目录都具有自己的系统分配的托管标识，必须向其授予 Power BI 租户的访问权限才能启用扫描。 目录名称可用于在 Azure 门户上查找其标识。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索 Azure Active Directory。
1. 在 Azure Active Directory 中创建一个新的安全组，方法是 [使用 Azure Active Directory 创建一个基本组并添加成员](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

    > [!Tip]
    > 如果你已有要使用的安全组，则可以跳过此步骤。

1. 请确保选择 "安全" 作为 " **组类型**"。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="安全组类型":::

1. 依次选择 "成员" 和 " **添加成员**"，将目录的托管标识添加到此安全组。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="将目录的托管实例添加到组":::

1. 搜索你的目录并将其选中。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="通过搜索来添加目录":::

1. 你应该会看到一条成功通知，显示它已添加。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="添加目录 MSI 成功":::

## <a name="associate-the-security-group-with-power-bi"></a>将安全组与 Power BI 相关联

1. 登录到 [Power BI 管理门户](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1)。 附加此功能标志：  `allowServicePrincipalsUseReadAdminAPIsUI=1` 。 此标志启用允许你关联安全组的功能。 例如，

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > 你需要成为 Power BI 管理员才能看到 "租户设置" 页。

1. 选择 "**开发人员设置**" "  >  **允许服务主体使用只读 Power BI api (预览版)**"。
1. 选择 " **特定安全组**"。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="显示如何允许服务主体获得只读 Power BI 管理 API 权限的图像 ":::

    > [!Caution]
    > 如果允许创建的安全组 (将数据目录托管标识作为成员) 使用只读 Power BI 管理 Api，则还允许该安全组访问此租户中所有 ) 项目的元数据 (例如仪表板和报表名称、所有者、说明等。 将元数据提取到 Azure 监控范围中后，监控范围的权限（而不是 Power BI 权限）确定可以查看该元数据的用户。

    > [!Note]
    > 你可以从开发人员设置中删除安全组，但不会从监控范围帐户中删除之前提取的元数据。 您可以单独删除它。

## <a name="register-power-bi-and-set-up-a-scan"></a>注册 Power BI 并设置扫描

现在，你已将目录权限指定为连接到 Power BI 租户的管理员 API，你需要在目录中设置扫描。 为此，请配置并运行 PowerShell 脚本。

1. 下载并提取 ADC PowerShell cmdlet。
1. 通过为脚本顶部的分配提供值来配置脚本。

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > 您必须是在其下运行命令的订阅的参与者或所有者。

1. 通过运行以下脚本开始扫描：

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>注册和扫描 Power BI

建议的身份验证方法为 MSI。 但是，若要扫描不同于你的目录的 Azure 租户中的 Power BI 租户，请使用委派的身份验证。

若要执行委派的身份验证，必须具有管理员用户凭据，并 Power BI 管理员凭据。 还必须创建 Azure 应用并将其授予 Power BI ReadAll 权限。

1. 导航到 [Azure 门户](https://portal.azure.com) 并搜索 " **应用注册**"。

1. 在 **应用注册** 中，选择 " **+ 新注册**"。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="显示如何创建新的 Azure 应用注册的图像":::

1. 输入应用名称。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="注册新应用":::

1. 创建应用后，选择 " **API 权限**"，然后单击 " **添加权限**"。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="显示如何向应用程序添加权限的图像":::

1. 选择 "**请求 API 权限** 上的 **Power BI 服务**"。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="显示如何选择 PBI 服务的图像":::

1. 选择 " **委托的权限** " 和 " **租户**"。 然后选择“添加权限”。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="显示如何请求 API 权限的图像":::

1. 选择“授予管理员许可”

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="显示如何授予管理员许可的图像":::

1. **(客户端) id** 和 **目录 (租户) ID** 值复制应用程序。  设置扫描时，将使用这些值。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="显示复制客户端和租户 Id 的图像":::

1. 在 PowerShell 中配置扫描。 脚本将提示输入凭据。 你必须在要使用的 Azure 订阅上至少具有 "参与者" 角色和 "监控范围" 数据源管理员角色。

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. 运行扫描。

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 监控范围，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
