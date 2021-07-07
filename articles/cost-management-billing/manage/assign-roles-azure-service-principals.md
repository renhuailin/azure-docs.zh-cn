---
title: 向 Azure 企业协议服务主体名称分配角色
description: 本文使用 PowerShell 和 REST API 帮助你将角色分配到服务主体名称。
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 05/01/2021
ms.author: banders
ms.openlocfilehash: 395f6804e0fdea88e65879817b83b9a8aabdd0f1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111748060"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>向 Azure 企业协议服务主体名称分配角色

可以在 [Azure 企业门户](https://ea.azure.com/)中管理企业协议 (EA) 注册。 可以创建不同的角色来管理组织、查看成本和创建订阅。 本文将 Azure PowerShell 和 REST API 与 Azure 服务主体名称 (SPN) 配合使用，帮助你自动执行其中一些任务。

在开始之前，请确保熟悉以下文章的内容：

- [企业协议角色](understand-ea-roles.md)
- [使用 Azure PowerShell 进行登录](/powershell/azure/authenticate-azureps)
- [如何使用 Postman 调用 REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>创建服务主体并对其进行身份验证

若要使用 SPN 自动执行 EA 操作，需要创建一个 Azure Active Directory (Azure AD) 应用程序。 该应用程序能够自动进行身份验证。

按照这些文章中的步骤来创建服务主体并对其进行身份验证。

- [创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
- [获取用于登录的租户和应用 ID 值](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

下面是应用程序注册页的一个示例。

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="显示“注册应用程序”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>查找 SPN 和租户 ID

还需要 SPN 的对象 ID 以及应用的租户 ID。 本文后面的权限分配操作需要此信息。

1. 打开 Azure Active Directory，然后选择“企业应用程序”。
1. 在列表中找到你的应用。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="显示示例企业应用程序的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

1. 选择应用以查找应用程序 ID 和对象 ID：

   :::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="显示企业应用程序的应用程序 ID 和对象 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

1. 请转到 Microsoft Azure AD“概述”页，找到租户 ID。

   :::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="显示租户 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

>[!NOTE]
>你的租户 ID 可能在其他位置称为主体 ID、SPN 或对象 ID。 Azure AD 租户 ID 的值类似于采用以下格式的 GUID：`11111111-1111-1111-1111-111111111111`。

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>可分配给 SPN 的权限

在本文的后面部分中，你将使用 EA 角色授予 Azure AD 应用执行操作的权限。 你只能将以下角色分配给 SPN，并且需要角色定义 ID，正如以下所示。

| 角色 | 允许的操作 | 角色定义 ID |
| --- | --- | --- |
| EnrollmentReader | 可以查看所有帐户和订阅的使用情况和费用。 可以查看注册相关的 Azure 预付（以前称为货币承诺）余额。 | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| EA 买方 | 购买预留订单和查看预留交易。 可以查看所有帐户和订阅的使用情况和费用。 可以查看注册相关的 Azure 预付（以前称为货币承诺）余额。 | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | 下载角色管理的部门的使用情况详细信息。 可以查看与角色的部门相关的使用情况和费用。 | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | 在给定的帐户范围内创建新订阅。 | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- 只能由具有注册写入者角色的用户将 EnrollmentReader 分配给 SPN。
- 只有具有注册写入者角色或部门写入者角色的用户才能将 DepartmentReader 角色分配给 SPN。
- 只有作为注册帐户所有者的用户才能将 SubscriptionCreator 角色分配给 SPN。 该角色不会显示在 EA 门户中。 它通过编程方式创建，仅供编程使用。
- EA 买方角色不会显示在 EA 门户中。 它通过编程方式创建，仅供编程使用。

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>向 SPN 分配注册帐户角色权限

1. 阅读[角色分配 - Put](/rest/api/billing/2019-10-01-preview/role-assignments/put) REST API 文章。 阅读此文时，请选择“试用”以通过 SPN 开始使用。

   :::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="显示 Put 文章中“试用”选项的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

1. 使用你的帐户凭据通过要分配的注册访问权限登录到租户。

1. 提供以下参数作为 API 请求的一部分。

   - `billingAccountName`：此参数为“计费帐户 ID”。 可以在 Azure 门户中的“成本管理 + 计费”概述页面上找到此 ID。

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="显示“计费帐户 ID”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`：此参数是需要提供的唯一 GUID。 可以使用 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell 命令生成 GUID。 也可使用[在线 GUID/UUID 生成器](https://guidgenerator.com/)网站生成唯一的 GUID。

   - `api-version`：使用 2019-10-01-preview 版本。 使用[角色分配 - Put - 示例](/rest/api/billing/2019-10-01-preview/role-assignments/put#examples)中的示例请求正文。

      请求正文具有 JSON 代码，其中包含需要使用的三个参数。

      | 参数 | 查找位置 |
      | --- | --- |
      | `properties.principalId` | 这是对象 ID 的值。 请参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
      | `properties.principalTenantId` | 请参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` |

      计费帐户名称是在 API 参数中使用的同一参数。 它是 EA 门户和 Azure 门户中显示的注册 ID。

      请注意，`24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` 是 EnrollmentReader 的计费角色定义 ID。

1. 选择“运行”以启动命令。

   :::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="显示示例角色分配 put 命令、“试用”和准备好运行的示例信息的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

   `200 OK` 响应显示已成功添加 SPN。

现在你可以使用 SPN 自动访问 EA API。 该 SPN 具有 EnrollmentReader 角色。

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>将 EA 买方角色权限分配给 SPN

对于 EA 买方角色，请对注册读者使用相同的步骤。 使用以下示例指定 `roleDefinitionId`：

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

## <a name="assign-the-department-reader-role-to-the-spn"></a>将部门读取者角色分配给 SPN

1. 请阅读[注册部门角色分配 - Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put) REST API 文章。 阅读此文时，请选择“试用”。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="显示“注册部门角色分配 - Put”文章中“试用”选项的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. 使用你的帐户凭据通过要分配的注册访问权限登录到租户。

1. 提供以下参数作为 API 请求的一部分。

   - `billingAccountName`：此参数为“计费帐户 ID”。 可以在 Azure 门户中的“成本管理 + 计费”概述页面上找到此 ID。

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="显示“计费帐户 ID”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`：此参数是需要提供的唯一 GUID。 可以使用 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell 命令生成 GUID。 也可使用[在线 GUID/UUID 生成器](https://guidgenerator.com/)网站生成唯一的 GUID。

   - `departmentName`：此参数是部门 ID。 可以在 Azure 门户中的“成本管理 + 计费” > “部门”页上查看部门 ID 。

      本示例使用了 ACE 部门。 本示例的 ID 为 `84819`。

      :::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="显示示例部门 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

   - `api-version`：使用 2019-10-01-preview 版本。 使用[注册部门角色分配 - Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put) 中的示例。

      请求正文具有 JSON 代码，其中包含需要使用的三个参数。

      | 参数 | 查找位置 |
      | --- | --- |
      | `properties.principalId` | 这是对象 ID 的值。 请参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
      | `properties.principalTenantId` | 请参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a` |

      计费帐户名称是在 API 参数中使用的同一参数。 它是 EA 门户和 Azure 门户中显示的注册 ID。

      计费角色定义 ID `db609904-a47f-4794-9be8-9bd86fbffd8a` 用于部门读者。

1. 选择“运行”以启动命令。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="显示示例注册部门角色分配 - Put REST、“试用”和准备好运行的示例信息的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

   `200 OK` 响应显示已成功添加 SPN。

现在你可以使用 SPN 自动访问 EA API。 该 SPN 具有 DepartmentReader 角色。

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>将订阅创建者角色分配给 SPN

1. 阅读[注册帐户角色分配 - Put](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put) 文章。 阅读时，请选择“试用”以将订阅创建者角色分配给 SPN。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="显示“注册帐户角色分配 - Put”文章中“试用”选项的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. 使用你的帐户凭据通过要分配的注册访问权限登录到租户。

1. 提供以下参数作为 API 请求的一部分。 阅读[注册帐户角色分配 - Put - URI 参数](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put#uri-parameters)文章。

   - `billingAccountName`：此参数为“计费帐户 ID”。 可以在 Azure 门户中的“成本管理 + 计费”概述页上找到此 ID。

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="显示“计费帐户 ID”的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`：此参数是需要提供的唯一 GUID。 可以使用 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell 命令生成 GUID。 也可使用[在线 GUID/UUID 生成器](https://guidgenerator.com/)网站生成唯一的 GUID。

   - `enrollmentAccountName`：此参数是帐户 ID。 可在 Azure 门户中“成本管理 + 计费”页上找到帐户名的帐户 ID。

      本示例使用了“GTM 测试帐户”。 ID 为 `196987`。

      :::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="显示帐户 ID 的屏幕截图。" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

   - `api-version`：使用 2019-10-01-preview 版本。 使用[注册部门角色分配 - Put - 示例](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put#examples)中的示例。

      请求正文具有 JSON 代码，其中包含需要使用的三个参数。

      | 参数 | 查找位置 |
      | --- | --- |
      | `properties.principalId` | 这是对象 ID 的值。 请参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
      | `properties.principalTenantId` | 请参阅[查找 SPN 和租户 ID](#find-your-spn-and-tenant-id)。 |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71` |

      计费帐户名称是在 API 参数中使用的同一参数。 它是 EA 门户和 Azure 门户中显示的注册 ID。

      计费角色定义 ID `a0bcee42-bf30-4d1b-926a-48d21664ef71` 用于订阅创建者角色。

1. 选择“运行”以启动命令。

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="显示“注册帐户角色分配 - Put”文章中“试用”选项的屏幕截图" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

   `200 OK` 响应显示已成功添加 SPN。

现在你可以使用 SPN 自动访问 EA API。 该 SPN 具有 SubscriptionCreator 角色。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure EA 门户管理](ea-portal-administration.md)。
