---
title: 为 Azure API for FHIR 配置 Azure 基于角色的访问控制 (Azure RBAC)
description: 本文介绍如何为 Azure API for FHIR 数据平面配置 Azure RBAC
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: 124dec2ec765628f08bdad18f4fd12d30141d5c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778248"
---
# <a name="configure-azure-rbac-for-fhir"></a>为 FHIR 配置 Azure RBAC 

在本文中，你将了解如何使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/index.yml) 来分配对 Azure API for FHIR 数据平面的访问权限。 如果数据平面用户是在与你的 Azure 订阅关联的 Azure Active Directory 租户中进行管理的，那么首选使用 Azure RBAC 来分配数据平面访问权限。 如果你使用的是外部 Azure Active Directory 租户，请参阅[本地 RBAC 分配引用](configure-local-rbac.md)。

## <a name="confirm-azure-rbac-mode"></a>确认 Azure RBAC 模式

为使用 Azure RBAC，必须将 Azure API for FHIR 的数据平面配置为使用 Azure 订阅租户，并且不应有指定的标识对象 ID。 可以通过检查 Azure API for FHIR 的“身份验证”边栏选项卡来验证设置：

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="确认 Azure RBAC 模式":::

“颁发机构”应设置为与订阅相关联的 Azure Active Directory 租户，并且标记有“允许的对象 ID”的框中不应有 GUID。 你还会注意到，该框已禁用，并且有一个标签指示应该使用 Azure RBAC 来分配数据平面角色。

## <a name="assign-roles"></a>分配角色

若要向用户、服务主体或组授予对 FHIR 数据平面的访问权限，请依次单击“访问控制(IAM)”、“角色分配”和“+ 添加”：

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="添加 Azure 角色分配":::

在“角色”选择中，搜索 FHIR 数据平面的内置角色之一：

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="内置 FHIR 数据角色":::

你可以选择：

* FHIR 数据读者：可以读取（和搜索）FHIR 数据。
* FHIR 数据写入者：可以读取、写入和软删除 FHIR 数据。
* FHIR 数据导出者：可以读取和导出（`$export` 运算符）数据。
* FHIR 数据参与者：可以执行所有数据平面操作。

在“选择”框中，搜索要为其分配角色的用户、服务主体或组。

>[!Note]
>请确保客户端应用程序注册已完成。 有关详细信息，请参阅[应用程序注册](register-confidential-azure-ad-client-app.md)。如果使用 OAuth 2.0 授权代码授予类型，请向用户授予相同的 FHIR 应用程序角色。 如果使用 OAuth 2.0 客户端凭据授予类型，则不需要此步骤。

## <a name="caching-behavior"></a>缓存行为

Azure API for FHIR 将决策最多缓存 5 分钟。 如果将用户添加到允许的对象 ID 列表，从而授予用户对 FHIR 服务器的访问权限，或者将用户从列表中删除，则预计需要 5 分钟的时间来传播权限的变化。

## <a name="next-steps"></a>后续步骤

本文介绍了如何为 FHIR 数据平面分配 Azure 角色。 若要了解 Azure API for FHIR 的其他设置，请参阅：
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 的其他设置](azure-api-for-fhir-additional-settings.md)
