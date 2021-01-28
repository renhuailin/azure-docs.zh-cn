---
title: 为托管的测试驱动器设置 Azure Marketplace 订阅
description: 介绍如何为 Dynamics 365 设置 Azure Marketplace 订阅，以实现客户参与，并使用 Dynamics 365 进行操作测试驱动器
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/09/2020
ms.openlocfilehash: 60eeceac916a7f8c64214b7a74a8cf60fd1ec8ac
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986118"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>为托管的测试驱动器设置 Azure Marketplace 订阅

本文介绍如何设置 Azure Marketplace 订阅和 **dynamics 365 For Customer Engagement** 或 **dynamics 365** For test 驱动器的操作环境。

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>为客户参与设置 Dynamics 365

1. 使用管理员帐户登录到 [Azure 门户](https://portal.azure.com/) 。
2. 通过将鼠标悬停在右上角的帐户图标上，验证你是否处于与 Dynamics 365 测试驱动器实例相关联的租户中。 如果你不在正确的租户中，请选择帐户图标以切换到正确的租户。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="选择正确的租户。":::

3. 验证 **Dynamics 365 Customer Engagement 计划** 许可证是否可用。

    [![检查计划许可证。](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. 在 Azure 中创建 Azure Active Directory (AD) 应用。 AppSource 将使用此应用在租户中预配和取消预配测试驱动器用户。
    1. 从 "筛选器" 窗格中，选择 **Azure Active Directory**。
    2. 选择“应用注册” 。

        [![选择 "应用注册"。](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. 选择“新注册”。
    4. 提供相应的应用程序名称。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="注册应用程序。":::

    5. 在 "受支持的帐户类型" 下，选择 " **帐户"**。
    6. 选择 " **创建** " 并等待应用创建完毕。
    7. 创建应用后，请记下 "概述" 屏幕上显示的 **应用程序 ID** 。 稍后配置测试驱动器时将需要此值。
    8. 若要添加 nativeclient-ios 的重定向 URI，请选择 " **身份验证** " 边栏选项卡。 在 "**平台配置**" 下，选择 "**添加平台**  >  **移动**  >  **桌面** 应用程序磁贴"。 选择 **nativeclient-ios** 重定向 URI，并选择 " **配置**"。

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="添加 nativeclient-ios 重定向 URI。":::

    9. 在 " **管理应用程序**" 下，选择 " **API 权限**"。
    10. 选择 " **添加权限** "，然后 **Microsoft Graph API**。
    11. 选择 " **应用程序** 权限" 类别，然后选择 " **目录. 全部** " 和 "目录"。 **所有** 权限。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="设置应用程序权限。":::

    12. 若要为 allow list Azure AD 应用添加 **DYNAMICS CRM-User 模拟** 访问权限，请再次选择 " **添加权限** "。

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="请求应用程序权限。":::

    13. 添加权限后，选择 " **授予 Microsoft 管理员同意**"。
    14. 从消息警报中选择 **"是"**。

        [![显示成功授予的应用程序权限。](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. 为 Azure AD 应用生成机密：
        1. 从 " **管理应用程序**" 中，选择 " **证书和密钥**"。
        2. 在 "客户端密码" 下，选择 " **新建客户端密码**"。
        3. 输入描述，例如 " *测试驱动器*"，并选择适当的持续时间。 此密钥过期后，测试驱动器将中断，此时需要生成并为 AppSource 新密钥。
        4. 选择 " **添加** " 以生成 Azure 应用密钥。 复制此值，因为在 lave 此边栏选项卡后会立即将其隐藏。 稍后配置测试驱动器时将需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="添加客户端密码。":::

5. 有时，将用户从 Azure AD 同步到 CRM 实例所花费的时间比预期要长。 为了帮助进行此操作，我们添加了一个强制同步用户的过程，但它要求 Azure AD 的应用程序 allowlisted 合作伙伴中心。 若要执行此操作，请参阅 [用户与客户参与实例同步](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md)。
6. 将服务主体角色添加到应用程序，以允许 Azure AD 应用从 Azure 租户中删除用户。
    1. 打开管理级 PowerShell 命令提示符。
    2. 如果未安装 MSOnline) ，则 Install-Module MSOnline (运行此命令。
    3. Connect-MsolService (这会显示一个弹出窗口;用新创建的组织租户) 登录。
    4. $applicationId = **<YOUR_APPLICATION_ID>**。
    5. $sp = Get-MsolServicePrincipal AppPrincipalId $applicationId。
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp。ObjectId-RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登录到你的帐户。":::

7. 将上面创建的 Azure 应用作为应用程序用户添加到你的测试驱动器 CRM 实例。
    1. 在 **Azure Active Directory** 中添加新用户。 只有 (属于同一租户) 的 **名称** 和 **用户名** 值才能创建此用户，并将其他字段保留为默认值。 复制 "用户名" 值。
    2. 登录到 **CRM 实例**，并选择 "**设置**  >  **安全**  >  **用户**"。
    3. 将视图更改为 **应用程序用户**。

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="设置用户的帐户信息。":::

    4. 添加新用户 (确保窗体适用于应用程序用户) 。
    5. 在 " **主电子邮件** " 和 " **用户名** " 字段中输入相同的用户名。 在 **应用程序 ID** 中添加 **Azure ApplicationId** 。
    6. 请指定任意 **全名**。
    7. 选择“保存”。
    8. 选择 " **管理角色**"。
    9. 分配包含读、写和分配角色权限（如 *系统管理员*）的自定义或 OOB 安全角色。

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="选择角色特权。":::

    10. 向应用程序用户分配你为你的测试驱动器创建的自定义安全角色。

## <a name="set-up-for-dynamics-365-for-operations"></a>为操作设置 Dynamics 365

1. 使用管理员帐户登录到 [Azure 门户](https://portal.azure.com/) 。
2. 通过将鼠标悬停在右上角的帐户图标上，验证你是否处于与 Dynamics 365 测试驱动器实例相关联的租户中。 如果你不在正确的租户中，请选择帐户图标以切换到正确的租户。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="选择正确的租户。":::

3. 在 Azure 中创建 Azure AD 应用。 AppSource 将使用此应用在租户中预配和取消预配测试驱动器用户。
    1. 从 "筛选器" 窗格中，选择 **Azure Active Directory**。
    2. 选择“应用注册” 。

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="选择应用注册。":::

    3. 选择“新注册”。
    4. 提供相应的应用程序名称。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="注册应用程序。":::

    5. 在 "受支持的帐户类型" 下，选择 " **帐户"**。
    6. 选择 " **创建** " 并等待应用创建完毕。
    7. 创建应用后，请记下 "概述" 屏幕上显示的 **应用程序 ID** 。 稍后配置测试驱动器时将需要此值。
    8. 在 " **管理应用程序**" 下，选择 " **API 权限**"。
    9. 选择 " **添加权限** "，然后 **Microsoft Graph API**。
    10. 选择 " **应用程序** 权限" 类别，然后选择 " **目录. 全部** " 和 "目录"。 **所有** 权限。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="设置应用程序权限。":::

    11. 选择 " **添加权限**"。
    12. 添加权限后，选择 " **授予 Microsoft 管理员同意**"。
    13. 从消息警报中选择 **"是"**。

        [![显示已成功授予的应用程序权限。](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. 为 Azure AD 应用生成机密：
        1. 从 " **管理应用程序**" 中，选择 " **证书和密钥**"。
        2. 在 "客户端密码" 下，选择 " **新建客户端密码**"。
        3. 输入描述，例如 " *测试驱动器*"，并选择适当的持续时间。 此密钥过期后，测试驱动器将中断，此时需要生成并为 AppSource 新密钥。
        4. 选择 " **添加** " 以生成 Azure 应用密钥。 复制此值，因为在 lave 此边栏选项卡后会立即将其隐藏。 稍后配置测试驱动器时将需要此值。

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="添加客户端密码。":::

4. 将服务主体角色添加到应用程序，以允许 Azure AD 应用从 Azure 租户中删除用户。
    1. 打开管理级 PowerShell 命令提示符。
    2. 如果未安装 MSOnline) ，则 Install-Module MSOnline (运行此命令。
    3. Connect-MsolService (这会显示一个弹出窗口;用新创建的组织租户) 登录。
    4. $applicationId = **<YOUR_APPLICATION_ID>**。
    5. $sp = Get-MsolServicePrincipal AppPrincipalId $applicationId。
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp。ObjectId-RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="登录到你的帐户。":::

5. 现在，将上述应用添加到 **Dynamics 365** ，使应用能够管理用户。
    1. 查找 **操作实例的 Dynamics 365** 。
    2. 在左上角，单击三行菜单 (汉堡) 。
    3. 选择 " **系统管理**"。
    4. 选择 **Azure Active Directory 应用程序**。
    5. 选择“+ 新建”  。
    6. 输入将执行代表操作的 **Azure AD 应用的客户端 ID** 。

    > [!NOTE]
    > 要执行操作的用户 ID (通常是实例的系统管理员或有权添加其他用户) 的用户。

    [![要执行操作的用户 ID (通常是实例的系统管理员或有权添加其他用户) 的用户。](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
