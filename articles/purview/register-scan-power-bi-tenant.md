---
title: 注册并扫描 Power BI 租户（预览版）
description: 了解如何使用 Azure Purview 门户注册并扫描 Power BI 租户。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: c29070f85fe0024113b6d5d4857733b23b522615
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271438"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>注册并扫描 Power BI 租户（预览版）

本文介绍如何使用 Azure Purview 门户注册并扫描 Power BI 租户。

> [!Note]
> 如果 Purview 实例和 Power BI 租户位于同一 Azure 租户中，则只能使用托管标识 (MSI) 身份验证来设置 Power BI 租户扫描。 

## <a name="create-a-security-group-for-permissions"></a>为权限创建安全组

若要设置身份验证，请创建一个安全组，并向其添加 Purview 托管标识。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索“Azure Active Directory”。
1. 按照[使用 Azure Active Directory 创建基本组并添加成员](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)中的说明在 Azure Active Directory 中创建一个新安全组。

    > [!Tip]
    > 如果你已有想要使用的安全组，则可以跳过此步骤。

1. 选择“安全性”作为“组类型” 。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="安全组类型的屏幕截图。":::

1. 将 Purview 托管标识添加到此安全组。 选择“成员”，然后选择“+ 添加成员” 。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="显示如何将目录的托管实例添加到组的屏幕截图。":::

1. 搜索 Purview 托管标识并将其选中。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="显示如何通过搜索目录名称来添加目录的屏幕截图。":::

    你应会看到一条成功通知，显示已添加该标识。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="显示已成功添加目录 MSI 的屏幕截图。":::

## <a name="associate-the-security-group-with-the-tenant"></a>将安全组与租户关联

1. 登录到 [Power BI 管理门户](https://app.powerbi.com/admin-portal/tenantSettings)。
1. 选择“租户设置”页。

    > [!Important]
    > 你需要成为 Power BI 管理员才能看到“租户设置”页。

1. 选择“管理员 API 设置” > “允许服务主体使用只读 Power BI 管理员 API (预览)” 。
1. 选择“特定安全组”。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="该图显示如何允许服务主体获取只读 Power BI 管理员 API 权限。":::

1. 选择“管理员 API 设置” > “使用详细元数据增强管理员 API 响应”，然后启用切换按钮，以允许 Purview 数据映射在扫描过程中自动发现 Power BI 数据集的详细元数据 

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-sub-artifacts.png" alt-text="该图显示用于启用子项目扫描的 Power BI 管理门户配置。":::

    > [!Caution]
    > 如果允许创建的安全组（其成员包括 Purview 托管标识）使用只读 Power BI 管理员 API，则还会允许该安全组访问此租户中的所有 Power BI 项目的元数据（例如仪表板和报表名称、所有者、说明等）。 将元数据拉取到 Azure Purview 后，Purview 的权限（而不是 Power BI 权限）会确定谁可以看到该元数据。

    > [!Note]
    > 可以从开发人员设置中删除安全组，但不会从 Purview 帐户中删除之前提取的元数据。 如果愿意，可以单独将其删除。

## <a name="register-your-power-bi-and-set-up-a-scan"></a>注册 Power BI 并设置扫描

授予用于连接到 Power BI 租户的管理员 API 的 Purview 托管标识权限后，现在，可以通过 Azure Purview Studio 设置扫描。

1. 在左侧导航中选择“数据映射”。

1. 然后选择“注册”。

    选择“Power BI”作为数据源。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="显示可供选择的数据源列表的图像。":::

3. 为 Power BI 实例指定一个易记名称。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="显示 Power BI 数据源易记名称的图像。":::

    该名称的长度必须介于 3-63 个字符之间，并且只能包含字母、数字、下划线和连字符。  不允许包含空格。

    默认情况下，系统将查找存在于同一 Azure 订阅中的 Power BI 租户。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="显示已注册的 Power BI 数据源的图像。":::

    > [!Note]
    > 对于 Power BI，只允许对一个实例进行数据源注册和扫描。


4. 为扫描提供一个名称。 然后选择包含或排除个人工作区的选项。 请注意，唯一受支持的身份验证方法是“托管标识”。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="显示 Power BI 扫描设置的图像。":::

    > [!Note]
    > * 切换扫描配置以包括或排除个人工作区将触发对 Power BI 源进行完全扫描

5. 在继续执行后续步骤之前，选择“测试连接”。 如果测试连接失败，请选择“查看报表”，以查看详细状态并排查问题 
    1. “访问 - 失败”状态表示用户身份验证失败。 使用托管标识的扫描将始终成功，因为无需用户身份验证。 [检查你是否提供了进行委派验证所需的适当身份验证](register-scan-power-bi-tenant.md#register-and-scan-a-cross-tenant-power-bi)
    1. “资产(+ 世系) - 失败”状态表示 Purview - Power BI 授权失败。 请确保 [Purview 托管标识已添加到 Power BI 管理门户中关联的安全组](register-scan-power-bi-tenant.md#create-a-security-group-for-permissions)
    1. “详细元数据(增强型) - 失败”状态表示对”使用详细元数据增强管理员 API 响应”设置禁用了 Power BI 管理门户
    
    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-test-connection-status-report.png" alt-text="测试连接状态报表页的屏幕截图。":::
 
1. 设置扫描触发器。 选项包括“一次”、“每 7 天一次”和“每 30 天一次”  。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Purview 扫描计划程序的屏幕截图。":::

6. 在“查看新扫描”上，选择“保存并运行”启动扫描 。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="显示保存并运行 Power BI 源的屏幕截图。":::

## <a name="register-and-scan-a-cross-tenant-power-bi"></a>注册并扫描跨租户的 Power BI 

在跨租户方案中，可使用 PowerShell 注册和扫描 Power BI 租户。 可通过 UI 体验使用 Azure Purview Studio 浏览和搜索远程租户的资产。 

如果 Power BI 租户所在的 Azure AD 租户不同于要在其中预配 Azure Purview 帐户的 Azure AD 租户，请考虑使用本指南。 在跨租户方案中，按照以下步骤在 Azure Purview 中注册和扫描一个或多个 Power BI 租户：

1. 下载[托管的扫描 PowerShell 模块](https://github.com/Azure/Purview-Samples/blob/master/Cross-Tenant-Scan-PowerBI/ManagedScanningPowerShell.zip)，并将其内容提取到所选择的位置。

2. 在计算机上，在 Windows 任务栏的搜索框中输入 PowerShell。 在搜索列表中，右键单击“Windows PowerShell”，然后选择“以管理员身份运行” 。


3. 在计算机中安装并导入模块（如果尚未安装）。

   ```powershell
    Install-Module -name az
    Import-Module -name az
    Login-AzAccount
    ```

4. 使用 Power BI 租户所在的 Azure AD 管理员凭据登录 Azure 环境。

   ```powershell
    Login-AzAccount
    ```

5. 在 PowerShell 窗口中输入以下命令，将 `<path-to-managed-scanning-powershell-modules>` 替换为提取的模块的文件夹路径，如 `C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell`

   ```powershell
   dir -Path <path-to-managed-scanning-powershell-modules> -Recurse | Unblock-File
   ```

6. 请输入以下命令来安装 PowerShell 模块。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell\Microsoft.DataCatalog.Management.Commands.dll'
   ```
   
7. 使用相同的 PowerShell 会话设置以下参数。 使用部署 Azure Purview 的 Azure AD 租户 ID 更新 `purview_tenant_id`，使用 Power BI 租户所在的 Azure AD 租户更新 `powerbi_tenant_id`，`purview_account_name` 为现有的 Azure Purview 帐户。
   
   ```powershell
   $azuretenantId = '<purview_tenant_id>'
   $powerBITenantIdToScan = '<powerbi_tenant_id>'
   $purviewaccount = '<purview_account_name>'
   ```
8. 创建跨租户服务主体。 

   1. 在 Power BI 所在的 Azure Active Directory 租户中创建应用注册。 请确保使用强密码更新 `password` 字段，使用托管 Power BI 租户的 Azure AD 租户中不存在的应用程序名称更新 `app_display_name`。

    ```powershell   
    $SecureStringPassword = ConvertTo-SecureString -String <'password'> -AsPlainText -Force
    $AppName = '<app_display_name>'
    New-AzADApplication -DisplayName $AppName -Password $SecureStringPassword
    ```
    
   2. 从 Azure Active Directory 仪表板中，选择新创建的应用程序，然后选择“应用注册”。 为应用程序分配以下委托的权限，并为租户授予管理员同意：
   
         - Power BI 服务     Tenant.Read.All
         - Microsoft Graph      openid

   3. 从 Azure Active Directory 仪表板中，选择新创建的应用程序，然后选择“身份验证”。 在“受支持的帐户类型”下，选择“任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户” 。 
   
   4. 在 Web 浏览器中运行以下 URL，为服务主体构造特定于租户的登录 URL：
   
     https://login.microsoftonline.com/<purview_tenant_id>/oauth2/v2.0/authorize?client_id=<client_id_to_delegate_the_pbi_admin>&scope=openid&response_type=id_token&response_mode=fragment&state=1234&nonece=67890
    
    请确保将参数替换为正确的信息：<purview_tenant_id> 是预配 Azure Purview 帐户的 Azure Active Directory 租户 ID (GUID)。
    <client_id_to_delegate_the_pbi_admin> 是与服务主体对应的应用程序 ID
   
   5. 使用任何非管理员帐户登录。 这是在外部租户中预配服务主体所必需的。
   
   6. 出现提示时，接受“查看你的基本配置文件”和“维护对已授予访问权限的数据的访问权限”所请求的权限 。

9. 使用新创建的应用程序的应用程序（客户端）ID 更新 `client_id_to_delegate_the_pbi_admin`，并在 PowerShell 会话中运行以下命令：
   
   ```powershell
   $ServicePrincipalId = '<client_id_to_delegate_the_pbi_admin>'
   ```

10.  在 Power BI 租户所在的 Azure Active Directory 租户中创建用户帐户，然后分配“Power BI 管理员”这一 Azure AD 角色。 使用相应的信息更新 `pbi_admin_username` 和 `pbi_admin_password`，并在 PowerShell 终端执行以下行：

        ```powershell
        $UserName = '<pbi_admin_username>'
        $Password = '<pbi_admin_password>'
        ```

11.  在 Azure Purview 订阅中，找到 Purview 帐户，然后使用 Azure RBAC 角色将 Purview 数据源管理员分配给服务主体和 Power BI 用户。

12. 若要将跨租户 Power BI 租户注册为 Azure Purview 帐户中的新数据源，请更新 `service_principal_key` 并在 PowerShell 会话中执行以下 cmdlet：

    ```powershell
    Set-AzDataCatalogSessionSettings -DataCatalogSession -TenantId $azuretenantId -ServicePrincipalAuthentication -ServicePrincipalApplicationId $ServicePrincipalId -ServicePrincipalKey '<service_principal_key>' -Environment Production -DataCatalogAccountName $purviewaccount

    Set-AzDataCatalogDataSource -Name 'pbidatasource' -AccountType PowerBI -Tenant $powerBITenantIdToScan -Verbose
    ```

13. 若要在 Azure Purview 中创建和运行新扫描，请在 PowerShell 会话中执行以下 cmdlet：

    ```powershell
    Set-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan' -AuthorizationType PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password  -IncludePersonalWorkspaces $true -Verbose

    Start-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan'
    ```
### <a name="known-limitations"></a>已知限制

-   对于跨租户方案，当前没有可用于跨 Power BI 租户进行注册和扫描的 UX 体验。
-   通过使用 Purview Studio 编辑在 PowerShell 中跨租户注册的 Power BI，将会篡改数据源注册并出现不一致的扫描行为。

        
## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
