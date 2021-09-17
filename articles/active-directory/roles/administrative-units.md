---
title: Azure Active Directory 中的管理单元 | Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元进行更精细的权限委派。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b91b216c6a813270cf3b30cde0d8450f964668
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734480"
---
# <a name="administrative-units-in-azure-active-directory"></a>Azure Active Directory 中的管理单元

本文介绍 Azure Active Directory (Azure AD) 中的管理单元。 管理单元是一种 Azure AD 资源，它可以是其他 Azure AD 资源的容器。 管理单元只能包含用户和组。

管理单元将角色中的权限限制为你定义的组织的任何部分。 例如，可以使用管理单元将[帮助台管理员](permissions-reference.md#helpdesk-administrator)角色委派给区域支持专家，以便他们仅管理其支持的区域中的用户。

## <a name="deployment-scenario"></a>部署场景

在由任何类型的独立部门组成的组织中，使用管理单元限制管理范围非常有用。 假设一个由多个自治学院（商业院、工程学院等）组成的大型大学示例。 每个学院都有一个 IT 管理员团队，他们负责控制访问、管理用户并为学校设置策略。

中心管理员可以：

- 为商学院创建管理单元。
- 仅可将商学院学生和员工填充到该管理单元。
- 创建仅对商学院管理单元中的 Azure AD 用户具有管理权限的角色。
- 将商学院 IT 团队及其范围添加到角色中。

## <a name="license-requirements"></a>许可要求

使用管理单元要求每个管理单元管理员具有 Azure AD Premium P1 许可证，要求管理单元成员具有 Azure AD Free 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版和高级版的正式发布功能](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

## <a name="manage-administrative-units"></a>对管理单元进行管理

可使用 Azure 门户、PowerShell cmdlet 和脚本或 Microsoft Graph 对管理单元进行管理。 有关详细信息，请参阅：

- [创建、删除、填充角色并将其添加到管理单元](admin-units-manage.md)：包括完整的操作过程。
- [使用管理单元](/powershell/azure/active-directory/working-with-administrative-units)：介绍如何使用 PowerShell 处理管理单元。
- [管理单元 Graph 支持](/graph/api/resources/administrativeunit)：提供有关适用于管理单元的 Microsoft Graph 的详细文档。

### <a name="plan-your-administrative-units"></a>规划管理单元

可使用管理单元对 Azure AD 资源进行逻辑分组。 IT 部门分散在世界各地的组织可能会创建定义相关地理边界的管理单元。 在另一种情况下，如果一个全球化组织的次级组织采用半自治运营模式，那么管理单元就可代表次级组织。

创建管理单元的条件将遵循组织的独特要求。 管理单元是跨 Microsoft 365 服务定义结构的常用方法。 建议你在准备管理单元时考虑它们在各项 Microsoft 365 服务中的使用。 如果可以在管理单元下跨 Microsoft 365 关联共有资源，则可以通过管理单元获取最大价值。

组织中管理单元的创建会经历以下阶段：

1. **初始采用**：组织将开始基于初始条件创建管理单元，并且随着条件的优化，管理单元的数量将增加。
1. **删除**：在定义条件后，不再需要的管理单元将被删除。
1. **稳定化**：定义组织结构之后，管理单元的数量在短期内不会发生显著变化。

## <a name="currently-supported-scenarios"></a>当前支持的场景

作为全局管理员或特权角色管理员，你可以使用 Azure 门户来执行以下操作：

- 创建管理单元
- 添加管理单元的用户和组成员
- 将 IT 人员分配给管理单元范围管理员角色。

管理单元范围管理员可以使用 Microsoft 365 管理中心对其管理单元中的用户进行基本管理。 管理单元范围组管理员可以使用 PowerShell、Microsoft Graph 和 Microsoft 365 管理中心来管理组。

>[!Note]
>Microsoft 365 管理中心仅提供本节中介绍的功能。 没有为管理单元范围 Azure AD 角色提供组织级功能。

以下各节描述了对管理单元场景的当前支持。

### <a name="administrative-unit-management"></a>管理单元管理

| 权限 |   Graph/PowerShell   | Azure 门户 | Microsoft 365 管理中心 |
| --- | --- | --- | --- |
| 创建和删除管理单元   |    支持    |   支持   |    不支持 |
| 单独添加和删除管理单位成员    |   支持    |   支持   |    不支持 |
| 使用 CSV 文件批量添加和删除管理单元成员   |    不支持     |  支持   |    无支持计划 |
| 分配管理单元范围管理员  |     支持    |   支持    |   不支持 |
| 基于属性动态添加和删除管理单元成员 | 不支持 | 不支持 | 不支持

### <a name="user-management"></a>用户管理

| 权限 |   Graph/PowerShell   | Azure 门户 | Microsoft 365 管理中心 |
| --- | --- | --- | --- |
| 用户属性、密码和许可证的管理单元范围管理   |    支持     |  支持   |   支持 |
| 用户登录的管理单元范围阻止和取消阻止    |   支持   |    支持   |    支持 |
| 用户多重身份验证凭据的管理单元范围管理   |    支持   |   支持   |   不支持 |

### <a name="group-management"></a>组管理

| 权限 |   Graph/PowerShell   | Azure 门户 | Microsoft 365 管理中心 |
| --- | --- | --- | --- |
| 组属性和成员的管理单元范围管理     |  支持   |    支持    |  不支持 |
| 组许可的管理单元范围管理   |    支持  |    支持   |   不支持 |

管理单元仅对管理权限应用范围。 它们不会阻止成员或管理员使用其[默认用户权限](../fundamentals/users-default-permissions.md)浏览管理单元外部的其他用户、组或资源。 在 Microsoft 365 管理中心，管理员的管理单元范围外的用户会被筛选掉。但你可以在 Azure 门户、PowerShell 和其他 Microsoft 服务中浏览其他用户。

## <a name="next-steps"></a>后续步骤

- [对管理单元进行管理](admin-units-manage.md)
- [对管理单元中的用户进行管理](admin-units-add-manage-users.md)
- [对管理单元中的组进行管理](admin-units-add-manage-groups.md)
- [向管理单元分配限定范围的角色](admin-units-assign-roles.md)
