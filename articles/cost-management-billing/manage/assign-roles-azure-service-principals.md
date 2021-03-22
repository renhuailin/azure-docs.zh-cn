---
title: 向 Azure 企业协议服务主体名称分配角色
description: 本文帮助你使用 PowerShell 和 REST API 向服务主体名称分配角色。
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: e7f5370e1e387947d196959fef31043ea8f4d3bd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508514"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>向 Azure 企业协议服务主体名称分配角色

可以在 [Azure 企业门户](https://ea.azure.com/)中管理企业协议 (EA) 注册。 可以创建不同的角色来管理组织、查看成本和创建订阅。 本文帮助你将 Azure PowerShell 和 REST API 与 Azure 服务主体名称 (SPN) 配合使用，从而自动执行其中一些任务。

在开始之前，请确保熟悉以下文章的内容：

- [企业协议角色](understand-ea-roles.md)
- [使用 Azure PowerShell 进行登录](/powershell/azure/authenticate-azureps)
- [如何使用 Postman 调用 REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>创建服务主体并对其进行身份验证

若要使用 SPN 自动执行 EA 操作，需要创建一个 Azure Active Directory (Azure AD) 应用程序。 该应用程序能够自动进行身份验证。 请阅读以下文章，并遵循其中的步骤创建服务主体并对其进行身份验证。

1. [创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [获取用于登录的租户和应用 ID 值](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

下面是显示应用程序注册的示例屏幕截图。

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="显示“注册应用程序”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>查找 SPN 和租户 ID

还需要 SPN 的对象 ID 以及应用的租户 ID。 在后面的部分，需要获取权限分配操作的信息。

可以在应用程序的概述页上找到 Azure AD 应用的租户 ID。 若要在 Azure 门户中找到该信息，请导航到“Azure Active Directory”并选择“企业应用程序”。 搜索应用。

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="显示示例企业应用程序的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

选择应用。 下面是显示应用程序 ID 和对象 ID 的示例。

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="显示企业应用程序的应用程序 ID 和对象 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

可以在 Microsoft Azure AD“概述”页上找到租户 ID。

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="显示在何处查看租户 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

在不同的位置，主体租户 ID 也称为主体 ID、SPN 和对象 ID。 Azure AD 租户 ID 的值类似于采用以下格式的 GUID：`11111111-1111-1111-1111-111111111111`。

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>可分配给 SPN 的权限

在后续步骤中，你将向 Azure AD 应用授予权限，以使用 EA 角色执行操作。 只能将以下角色分配给 SPN。 稍后在分配步骤中将使用角色定义 ID（与表中所示完全相同）。

| 角色 | 允许的操作 | 角色定义 ID |
| --- | --- | --- |
| EnrollmentReader | 可以查看所有帐户和订阅的使用情况和费用。 可以查看注册相关的 Azure 预付（以前称为货币承诺）余额。 | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | 下载角色管理的部门的使用情况详细信息。 可以查看与角色的部门相关的使用情况和费用。 | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | 在给定的帐户范围内创建新订阅。 | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- 只能由具有注册写入者角色的用户将注册读取者分配给 SPN。
- 只能由具有注册写入者角色或部门写入者角色的用户将部门读取者分配给 SPN。
- 只能由注册帐户的帐户所有者用户将订阅创建者角色分配给 SPN。

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>向 SPN 分配注册帐户角色权限

阅读[角色分配 - Put](/rest/api/billing/2019-10-01-preview/roleassignments/put) REST API 文章。

阅读此文时，请选择“试用”以开始使用 SPN。

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="显示 Put 文章中“试用”选项的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

使用帐户登录到有权访问要在其中分配访问权限的注册的租户。

提供以下参数作为 API 请求的一部分。

**billingAccountName**

该参数为计费帐户 ID。 可以在 Azure 门户中的“成本管理 + 计费”概述页上找到此 ID。

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="显示“计费帐户 ID”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

该参数是需要提供的唯一 GUID。 可以使用 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell 命令生成 GUID。

或者，可以使用[在线 GUID/UUID 生成器](https://guidgenerator.com/)网站生成唯一的 GUID。

**api-version**

使用 **2019-10-01-preview** 版本。

请求正文包含需要使用的 JSON 代码。

使用[角色分配 - Put - 示例](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples)中的示例请求正文。

需要将三个参数用作 JSON 的一部分。

| 参数 | 查找位置 |
| --- | --- |
| properties.principalId | 参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
| properties.principalTenantId | 参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

计费帐户名称是在 API 参数中使用的同一参数。 它是 EA 门户和 Azure 门户中显示的注册 ID。

请注意，`24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` 是 EnrollmentReader 的计费角色定义 ID。

选择“运行”以启动命令。

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="显示示例角色分配 put 命令、“试用”和准备好运行的示例信息的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

`200 OK` 响应显示已成功添加 SPN。

现在，可以使用该 SPN（具有对象 ID 的 Azure AD 应用）自动访问 EA API。 该 SPN 具有 EnrollmentReader 角色。

## <a name="assign-the-department-reader-role-to-the-spn"></a>将部门读取者角色分配给 SPN

在开始之前，请阅读[注册部门角色分配 - Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) REST API 文章。

阅读此文时，请选择“试用”。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="显示“注册部门角色分配 - Put”文章中“试用”选项的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

使用帐户登录到有权访问要在其中分配访问权限的注册的租户。

提供以下参数作为 API 请求的一部分。

**billingAccountName**

该参数是计费帐户 ID。 可以在 Azure 门户中的“成本管理 + 计费”概述页上找到此 ID。

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="显示“计费帐户 ID”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

该参数是需要提供的唯一 GUID。 可以使用 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell 命令生成 GUID。

或者，可以使用[在线 GUID/UUID 生成器](https://guidgenerator.com/)网站生成唯一的 GUID。

**departmentName**

该参数是部门 ID。 可以在 Azure 门户中看到部门 ID。 导航到“成本管理 + 计费”>“部门”。

本示例使用了 ACE 部门。 本示例的 ID 为 `84819`。

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="显示示例部门 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**api-version**

使用 **2019-10-01-preview** 版本。

请求正文包含需要使用的 JSON 代码。

使用[注册部门角色分配 - Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) 中的示例。 需要将三个参数用作 JSON 的一部分。

| 参数 | 查找位置 |
| --- | --- |
| properties.principalId | 参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
| properties.principalTenantId | 参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

计费帐户名称是在 API 参数中使用的同一参数。 它是 EA 门户和 Azure 门户中显示的注册 ID。

计费角色定义 ID `db609904-a47f-4794-9be8-9bd86fbffd8a` 用于部门读取者。

选择“运行”以启动命令。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="显示示例注册部门角色分配 - Put REST、“试用”和准备好运行的示例信息的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

`200 OK` 响应显示已成功添加 SPN。

现在，可以使用该 SPN（具有对象 ID 的 Azure AD 应用）自动访问 EA API。 该 SPN 具有 DepartmentReader 角色。

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>将订阅创建者角色分配给 SPN

阅读[注册帐户角色分配 - Put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) 文章。

阅读时，请选择“试用”以将订阅创建者角色分配给 SPN。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="显示“注册帐户角色分配 - Put”文章中“试用”选项的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

使用帐户登录到有权访问要在其中分配访问权限的注册的租户。

提供以下参数作为 API 请求的一部分。 阅读[注册帐户角色分配 - Put - URI 参数](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters)文章。

**billingAccountName**

该参数为计费帐户 ID。 可以在 Azure 门户中的“成本管理 + 计费”概述页上找到此 ID。

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="显示“计费帐户 ID”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

该参数是需要提供的唯一 GUID。 可以使用 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell 命令生成 GUID。

或者，可以使用[在线 GUID/UUID 生成器](https://guidgenerator.com/)网站生成唯一的 GUID。
**enrollmentAccountName**

该参数是帐户 ID。 在 Azure 门户中“成本管理 + 计费”页上的“注册和部门”范围内找到帐户名的帐户 ID。

本示例使用了“GTM 测试”帐户。 ID 为 `196987`。

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="显示帐户 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**api-version**

使用 **2019-10-01-preview** 版本。

请求正文包含需要使用的 JSON 代码。

使用[注册部门角色分配 - Put - 示例](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment)中的示例。

需要将三个参数用作 JSON 的一部分。

| 参数 | 查找位置 |
| --- | --- |
| properties.principalId | 参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
| properties.principalTenantId | 参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

计费帐户名称是在 API 参数中使用的同一参数。 它是 EA 门户和 Azure 门户中显示的注册 ID。

计费角色定义 ID `a0bcee42-bf30-4d1b-926a-48d21664ef71` 用于订阅创建者角色。

选择“运行”以启动命令。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="显示“注册帐户角色分配 - Put”文章中“试用”选项的屏幕截图" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

`200 OK` 响应显示已成功添加 SPN。

现在，可以使用该 SPN（具有对象 ID 的 Azure AD 应用）自动访问 EA API。 该 SPN 具有 SubscriptionCreator 角色。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure EA 门户管理](ea-portal-administration.md)。