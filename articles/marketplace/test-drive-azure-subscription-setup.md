---
title: 为托管体验版设置 Azure 市场订阅
description: 介绍如何为 Dynamics 365 for Customer Engagement 和 Dynamics 365 for Operations 体验版设置 Azure 市场订阅
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: c82f68ee35ae95a424c0847be9a9cc770185af43
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005728"
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
    2. 选择“应用注册” 。

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
        3. 输入描述，例如“体验版”，然后选择相应的持续时间。 此密钥过期后，体验版将中断，此时需要生成并为 AppSource 提供新的密钥。
        4. 选择“添加”以生成 Azure 应用机密。 复制此值，因为收起此边栏选项卡后会立即将其隐藏。 稍后配置体验版时将需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="添加客户端机密。":::

5. 将服务主体角色添加到应用程序，以允许 Azure AD 应用删除 Azure 租户中的用户。
    1. 打开管理级别的 PowerShell 命令提示符。
    2. Install-Module MSOnline（如果未安装 MSOnline，则运行此命令）。
    3. Connect-MsolService（这会显示一个弹出窗口；使用新创建的 org 租户登录）。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登录你的帐户。":::

6. 将上面创建的 Azure 应用作为应用程序用户添加到体验版 CRM 实例。
    1. 在 Azure Active Directory 中添加新用户。 创建此用户只需提供“名称”和“用户名”值（属于同一租户） ，将其他字段保留为默认值。 复制用户名值。
    2. 登录 CRM 实例，并选择“设置” > “安全” > “用户”。
    3. 将视图更改为“应用程序用户”。

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="设置用户的帐户信息。":::

    4. 添加新用户（确保窗体针对“应用程序用户”）。
    5. 在“主电子邮件”和“用户名”字段中输入相同的用户名。 在“应用程序 ID”中添加“Azure ApplicationId”。
    6. 指定任意“全名”。
    7. 选择“保存”。
    8. 选择“管理角色”。
    9. 分配包含读取和写入的自定义或 OOB 安全角色，并分配角色特权（例如“系统管理员”）。

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="选择角色特权。":::

    10. 此外，启用“代表其他用户”特权。
    11. 向应用程序用户分配为体验版创建的自定义安全角色。

## <a name="set-up-for-dynamics-365-for-operations"></a>针对 Dynamics 365 for Operations 的设置

1. 使用管理帐户登录 [Azure 门户](https://portal.azure.com/)。
2. 通过将鼠标悬停在右上角的帐户图标上，验证你是否处于与 Dynamics 365 体验版实例相关联的租户中。 如果不在正确的租户中，请选择帐户图标以切换到正确的租户中。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="选择正确的租户。":::

3. 在 Azure 中创建 Azure AD 应用。 AppSource 将使用此应用在租户中预配和取消预配体验版用户。
    1. 在筛选器窗格中，选择“Azure Active Directory”。
    2. 选择“应用注册” 。

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
        4. 选择“添加”以生成 Azure 应用机密。 复制此值，因为收起此边栏选项卡后会立即将其隐藏。 稍后配置体验版时将需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="添加客户端机密。":::

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
