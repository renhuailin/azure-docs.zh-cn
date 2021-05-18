---
title: Azure Red Hat OpenShift 的 Azure Active Directory 集成
description: 了解如何创建 Azure AD 安全组和用户，以便在 Microsoft Azure Red Hat OpenShift 群集上测试应用。
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f0bf28d61d4c9ad95a485fb4b60e370c16ace16c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633319"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的 Azure Active Directory 集成

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月 30 日停用。 对新建 Azure Red Hat OpenShift 3.11 群集的支持会持续到 2020 年 11 月 30 日。 在停用之后，剩余的 Azure Red Hat OpenShift 3.11 群集将会关闭，以防出现安全漏洞。
> 
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:arofeedback@microsoft.com)。

如果尚未创建 Azure Active Directory (Azure AD) 租户，请按照[创建 Azure Red Hat OpenShift 的 Azure AD 租户](howto-create-tenant.md)中的说明操作，然后再继续执行这些说明。

Microsoft Azure Red Hat OpenShift 需要权限，才能代表群集执行任务。 如果组织还没有要用作服务主体的 Azure AD 用户、Azure AD 安全组或 Azure AD 应用注册，请按照以下说明进行创建。

## <a name="create-a-new-azure-active-directory-user"></a>创建新的 Azure Active Directory 用户

在 [Azure 门户](https://portal.azure.com)中，确保租户显示在门户右上方你的用户名之下：

![门户右上方列出租户时的屏幕截图](./media/howto-create-tenant/tenant-callout.png)如果显示的租户错误，请单击右上方的用户名，然后单击“切换目录”，然后从“所有目录”列表中选择正确的租户。

创建新的 Azure Active Directory“所有者”用户，以登录到 Azure Red Hat OpenShift 群集。

1. 转到[用户-所有用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)边栏选项卡。
2. 单击“+ 新建用户”以打开“用户”窗格。
3. 输入此用户的名称。
4. 根据已创建租户的名称创建一个用户名，并在末尾追加 `.onmicrosoft.com`。 例如 `yourUserName@yourTenantName.onmicrosoft.com`。 记下此用户名。 需要使用它来登录到群集。
5. 单击“目录角色”以打开目录角色窗格，选择“所有者”，然后单击窗格底部的“确定”。
6. 在“用户”窗格中，单击“显示密码”并记录临时密码。 首次登录后，系统将提示你重置密码。
7. 在窗格底部，单击“创建”以创建用户。

## <a name="create-an-azure-ad-security-group"></a>创建 Azure AD 安全组

要授予群集管理员访问权限，需要将 Azure AD 安全组中的成员身份同步到 OpenShift 组“osa-customer-admins”。 如果未指定，则不会授予群集管理员访问权限。

1. 打开 [Azure Active Directory 组](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)边栏选项卡。
2. 单击“+新建组”。
3. 提供组名称和描述。
4. 将“组类型”设置为“安全”。 
5. 将“成员身份类型”设置为“已分配”。

    将在前面步骤中创建的 Azure AD 用户添加到此安全组。

6. 单击“成员”，以打开“选择成员”窗格 。
7. 在成员列表中，选择上面创建的 Azure AD 用户。
8. 在门户底部，单击“选择”，然后单击“创建”以创建安全组。

    写下组 ID 值。

9. 创建组后，所有组的列表中都会看到它。 单击该新组。
10. 在出现的页面上，复制下来“对象 ID”。 在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值作为 `GROUPID`。

> [!IMPORTANT]
> 要将此组与 osa-customer-admins OpenShift 组进行同步，请使用 Azure CLI 创建群集。 Azure 门户当前缺少用于设置此组的字段。

## <a name="create-an-azure-ad-app-registration"></a>创建 Azure AD 应用注册

在创建群集的过程中，通过省略 `az openshift create` 命令的 `--aad-client-app-id` 标志，可以自动创建 Azure Active Directory (Azure AD) 应用注册客户端。 本教程将介绍如何完整创建 Azure AD 应用注册。

如果组织尚无 Azure Active Directory (Azure AD) 应用注册来用作服务主体，请按照以下说明创建一个。

1. 打开[应用注册边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)，单击“+新建注册”。
2. 在“注册应用程序”窗格中，为应用程序注册输入一个名称。
3. 确保在“支持的帐户类型”下，已选择“仅此组织目录中的帐户” 。 这是最安全的选项。
4. 此后，在我们知道群集的 URI 后会再添加一个重定向 URI。 单击“注册”按钮，创建 Azure AD 应用程序注册。
5. 在出现的页面上，复制下来“应用程序（客户端）ID”。 在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值作为 `APPID`。

![应用对象页的屏幕截图](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>创建客户端机密

生成用于对应用进行身份验证的客户端密码，以便登录到 Azure Active Directory。

1. 在应用注册页的“管理”部分中，单击“证书和密码”。
2. 在“证书和密码”窗格中，单击“+新建客户端密码” 。  此时会显示“添加客户端密码”窗格。
3. 提供说明。
4. 将“过期时间”设置为所需的持续时间（例如 2 年后）。
5. 单击“添加”，该密钥值将显示到页面的“客户端密钥”部分。
6. 复制下来密钥值。 在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值作为 `SECRET`。

![“证书和密码”窗格的屏幕截图](./media/howto-create-tenant/create-key.png)

有关 Azure 应用程序对象的详细信息，请参阅“[Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)”。

有关创建新 Azure AD 应用程序的详细信息，请参阅“[使用 Azure Active Directory v1.0 终结点注册应用](../active-directory/develop/quickstart-register-app.md)”。

## <a name="add-api-permissions"></a>添加 API 权限

[//]: # (不要更改为 Microsoft Graph。它不适用于 Microsoft Graph。)
1. 在“管理”部分单击“API 权限” 
2. 单击“添加权限”，然后依次选择“Azure Active Directory Graph”和“委托的权限”。
> [!NOTE]
> 请确保已选择“Azure Active Directory Graph”，而不是“Microsoft Graph”图块。

3. 展开以下列表中的“用户”，并启用“User.Read”权限。 如果“User.Read”默认已启用，请确保是“Azure Active Directory Graph”权限“User.Read”。
4. 向上滚动并选择“应用程序权限”。
5. 展开以下列表中的“目录”，然后启用“Directory.ReadAll”。
6. 单击“添加权限”以接受更改。
7. 现在，API 权限面板应显示“User.Read”和“Directory.ReadAll”两种权限。 请注意“Directory.ReadAll”旁边“需要管理员同意”列中的警告。
8. 如果你是 Azure 订阅管理员，请单击下面的“对订阅名称授权管理员同意”。 如果你不是 Azure 订阅管理员，请向管理员请求同意。

![API 权限面板的屏幕截图。 已添加 User.Read 和 Directory.ReadAll 权限，对于 Directory.ReadAll 需要管理员同意](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 只有授权同意后，才能同步群集管理员组。 在“需要管理员同意”列，将会看到一个带复选标记的绿色圆圈和一条消息“已授权订阅名称” 。

有关管理管理员和其他角色的详细信息，请参阅[添加或更改 Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)。

## <a name="resources"></a>资源

* [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)
* [快速入门：向 Azure Active Directory v1.0 终结点注册应用](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>后续步骤

如果已满足所有 [Azure Red Hat OpenShift 先决条件](howto-setup-environment.md)，则可以创建第一个群集！

试用教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)