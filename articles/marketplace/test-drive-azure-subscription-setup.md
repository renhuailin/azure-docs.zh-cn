---
title: 为托管体验版设置 Azure 市场订阅
description: 介绍如何为 Dynamics 365 for Customer Engagement 和 Dynamics 365 for Operations 体验版设置 Azure 市场订阅
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 3aa0ddf4a9013d5f64584fbe93a795f6420dc410
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389896"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>为托管体验版设置 Azure 市场订阅

本文介绍如何为体验版设置 Azure 市场订阅以及 Dynamics 365 for Customer Engagement & Power Apps 或 Dynamics 365 for Operations 环境。 

## <a name="set-up-for-dynamics-365-for-customer-engagement--power-apps"></a>Dynamics 365 for Customer Engagement & Power Apps 的设置

1. 使用管理帐户登录 [Azure 门户](https://portal.azure.com/)。
2. 通过将鼠标悬停在右上角的帐户图标上，验证你是否处于与 Dynamics 365 体验版实例相关联的租户中。 如果不在正确的租户中，请选择帐户图标以切换到正确的租户中。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="选择正确的租户。":::

3. 验证 Dynamics 365 Customer Engagement 计划许可证是否可用。

    [![检查计划许可证。](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. 在 Azure 中创建 Azure Active Directory (AD) 应用。 AppSource 将使用此应用在租户中预配和取消预配体验版用户。
    1. 在筛选器窗格中，选择“Azure Active Directory”。
    2. 选择 **“应用注册”**。

        [![选择应用注册。](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. 选择“新注册”。
    4. 提供相应的应用程序名称。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="注册应用程序。":::

    5. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
    6. 选择“创建”，等待创建完应用。
    7. 创建应用后，请记下概述屏幕中显示的“应用程序 ID”。 稍后配置体验版时将需要此值。
    8. 在“管理应用程序”下选择“API 权限”。
    9. 选择“添加权限”，然后选择“Microsoft Graph API”。
    10. 选择“应用程序”权限类别，然后选择“User.ReadWrite.All”、“Directory.Read.All”和“Directory.ReadWrite.All”权限。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="设置应用程序权限。":::

    11. 添加权限后，选择“代表 Microsoft 授予管理员同意”。
    12. 在消息警报中，选择“是”。

        [![显示成功授予的应用程序权限。](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. 要为 Azure AD 应用生成机密：
        1. 在“管理应用程序”中，选择“证书和机密”。
        2. 在“客户端机密”下，选择“新建客户端机密”。

             :::image type="content" source="./media/test-drive/new-client-secret.png" alt-text="添加新的客户端密码。":::

        3. 输入描述，例如“体验版”，然后选择相应的持续时间。 由于此密钥过期后，测试驱动器将中断，此时需要为 AppSource 生成并提供一个新密钥，因此我们建议使用最长持续时间为 24 个月。
        4. 选择“添加”以生成 Azure 应用机密。 复制此值，因为此值会在收起此边栏选项卡后立即隐藏。 稍后配置体验版时将需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret-customer.png" alt-text="添加客户端机密。":::

5. 将服务主体角色添加到应用程序，以允许 Azure AD 应用删除 Azure 租户中的用户。 可通过两个选项完成此步骤。

    **选项 1**

    1. 搜索“Azure AD 角色和管理员”，然后选择该服务。

        :::image type="content" source="./media/test-drive/active-ad-roles.png" alt-text="显示如何搜索 Azure AD 角色和管理员。":::

    2. 在“所有角色”页上，搜索“用户管理员”角色，然后双击“用户管理员”  。

        :::image type="content" source="./media/test-drive/user-administrator.png" alt-text="显示如何搜索并选择“用户管理员”。":::

    3. 选择“添加分配”。

        :::image type="content" source="./media/test-drive/add-assignments-1.png" alt-text="显示“添加分配”按钮。":::

    4. 搜索并选择上面创建的应用，然后选择“添加”。

        :::image type="content" source="./media/test-drive/add-assignments-2.png" alt-text="显示成功的应用分配。":::

    5. 请注意，已成功向应用程序分配服务主体角色：

        :::image type="content" source="./media/test-drive/successful-assignment.png" alt-text="显示已成功向应用程序分配服务主体角色。":::

    **方法 2**

    1. 打开管理级别的 PowerShell 命令提示符。
    2. Install-Module MSOnline（如果未安装 MSOnline，则运行此命令）。
    3. Connect-MsolService（这会显示一个弹出窗口；使用新创建的 org 租户登录）。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

         :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登录你的帐户。":::

6. 新建安全组，并将其添加到画布应用 (Power Apps)。 此步骤仅适用于画布应用 (Power Apps) 产品/服务。
    1. 新建安全组。
        1. 转到 Azure Active Directory。
        1. 在“管理”下，选择“组” 。
        1. 选择“+ 新作用域”。
        1. 选择“安全组”类型。 
        1. 对于“组名称”，请输入“TestDriveSecurityGroup”。
        1. 添加描述，例如“体验版安全组”。
        1. 其余字段保留默认值，然后选择“创建”。

            :::image type="content" source="./media/test-drive/create-new-group.png" alt-text="显示如何新建安全组。":::

    1. 将刚刚创建的安全组添加到画布应用 (Power Apps)。
        1. 打开“PowerApps 门户”页并登录。
        1. 选择“应用”，然后选择应用中的省略号。
        1. 选择 **共享**。
        1. 搜索在上一步中创建的 TestDriveSecurityGroup 安全组。
        1. 向安全组授予数据权限。
        1. 取消选中“发送电子邮件”邀请复选框。
        1. 选择 **共享**。
    
            > [!NOTE]
            > 使用 CE/Dataverse for Canvas App (Power Apps) 以外的后端数据源时：
            > - 允许上面创建的安全组访问数据源。 例如，SharePoint 数据源。
            > - 打开 SharePoint 并与安全组共享数据表。

7. 将刚刚创建的 Azure 应用作为应用程序用户添加到体验版 CRM 实例。 此步骤仅适用于 Dynamics 365 客户参与产品/服务。
    1. 登录 CRM 实例，并选择“设置” > “安全” > “用户”。
    2. 将视图更改为“应用程序用户”。

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="设置用户的帐户信息。":::

    3. 添加新用户（确保窗体针对“应用程序用户”）。
    4. 在“应用程序 ID”中添加上面创建的“Azure ApplicationId”。
    5. 选择“保存”。
    6. 选择“管理角色”。
    7. 分配包含读取和写入的自定义或 OOB 安全角色，并分配角色特权（例如“系统管理员”）。

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="选择角色特权。":::

    10. 此外，启用“代表其他用户操作”特权。
    11. 向应用程序用户分配为体验版创建的自定义安全角色。

## <a name="set-up-for-dynamics-365-for-operations"></a>针对 Dynamics 365 for Operations 的设置

1. 使用管理帐户登录 [Azure 门户](https://portal.azure.com/)。
2. 通过将鼠标悬停在右上角的帐户图标上，验证你是否处于与 Dynamics 365 体验版实例相关联的租户中。 如果不在正确的租户中，请选择帐户图标以切换到正确的租户中。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="选择正确的租户。":::

3. 在 Azure 中创建 Azure AD 应用。 AppSource 将使用此应用在租户中预配和取消预配体验版用户。
    1. 在筛选器窗格中，选择“Azure Active Directory”。
    2. 选择 **“应用注册”**。

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="选择应用注册。":::

    3. 选择“新注册”。
    4. 提供相应的应用程序名称。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="注册应用程序。":::

    5. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
    6. 选择“创建”，等待创建完应用。
    7. 创建应用后，请记下概述屏幕中显示的“应用程序 ID”。 稍后配置体验版时将需要此值。
    8. 在“管理应用程序”下选择“API 权限”。
    9. 选择“添加权限”，然后选择“Microsoft Graph API”。
    10. 选择“应用程序”权限类别，然后选择“Directory.Read.All”和“Directory.ReadWrite.All”权限。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="设置应用程序权限。":::

    11. 选择“添加权限”。
    12. 添加权限后，选择“代表 Microsoft 授予管理员同意”。
    13. 在消息警报中，选择“是”。

        [![显示已成功授予应用程序权限。](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. 要为 Azure AD 应用生成机密：
        1. 在“管理应用程序”中，选择“证书和机密”。
        2. 在“客户端机密”下，选择“新建客户端机密”。
        3. 输入描述，例如“体验版”，然后选择相应的持续时间。 此密钥过期后，体验版将中断，此时需要生成并为 AppSource 提供新的密钥。
        4. 选择“添加”以生成 Azure 应用机密。 复制此值，因为此值会在收起此边栏选项卡后立即隐藏。 稍后配置体验版时将需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret-operations.png" alt-text="显示添加了客户端密码。":::

4. 将服务主体角色添加到应用程序，以允许 Azure AD 应用删除 Azure 租户中的用户。
    1. 打开管理级别的 PowerShell 命令提示符。
    2. Install-Module MSOnline（如果未安装 MSOnline，则运行此命令）。
    3. Connect-MsolService（这会显示一个弹出窗口；使用新创建的 org 租户登录）。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登录你的帐户。":::

5. 现在，将上述应用添加到 Dynamics 365 for Operations，以便使用此应用来管理用户。
    1. 查找 Dynamics 365 for Operations 实例。
    2. 在左上角，单击三线条菜单（汉堡）。
    3. 选择“系统管理”。
    4. 选择“Azure Active Directory 应用程序”。
    5. 选择“+ 新建”  。
    6. 输入将执行代表操作的“Azure AD 应用的客户端 ID”。

    > [!NOTE]
    > 将以其名义执行操作的用户 ID（通常是实例的系统管理员或有特权添加其他用户的用户）。

    [![将以其名义执行操作的用户 ID（通常是实例的系统管理员或有特权添加其他用户的用户）。](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
