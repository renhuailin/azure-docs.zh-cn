---
title: 为 FHIR 服务配置 Azure RBAC-Azure 医疗保健 Api
description: 本文介绍如何配置 FHIR 的 Azure RBAC。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: ff2a488a7ed8a693f23b533242748eed400bd802
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782488"
---
# <a name="configure-azure-rbac-for-the-fhir-service"></a>为 FHIR 服务配置 Azure RBAC

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本文中，你将了解如何使用 azure [RBAC)  (azure 基于角色的访问控制 ](../../role-based-access-control/index.yml) 来分配对医疗保健 api 数据平面的访问权限。 如果数据平面用户是在与你的 Azure 订阅关联的 Azure Active Directory 租户中进行管理的，那么首选使用 Azure RBAC 来分配数据平面访问权限。 

## <a name="assign-roles"></a>分配角色

若要授予用户、服务主体或组对 FHIR 数据平面的访问权限，请从 Azure 门户中选择 FHIR 服务。 选择 " **访问控制 (IAM")**，然后选择 " **角色分配** " 选项卡。选择 " **+ 添加**"，然后选择 " **添加角色分配**"。
 
如果角色分配选项灰显，请请求 Azure 订阅管理员向你授予对订阅或资源组的权限，例如 "用户访问管理员"。 有关 Azure 内置角色的详细信息，请参阅 [azure 内置角色](../../role-based-access-control/built-in-roles.md)。

[![访问控制角色分配。 ](media/rbac/role-assignment.png) ](media/rbac/role-assignment.png#lightbox)

在 "角色选择" 中，搜索 FHIR 数据平面的某个内置角色，例如 "FHIR 数据参与者"。 可以在下面选择其他角色。

* **FHIR 数据读取器**：可以读取 (和搜索) FHIR 数据。
* **FHIR 数据编写器**：可读取、写入和软删除 FHIR 数据。
* **FHIR 数据导出** 程序：可以读取和导出 ($export 运算符) 数据。
* **FHIR 数据参与者**：可执行所有数据平面操作。
* **FHIR 数据转换器**：可以使用转换器执行数据转换

在 " **选择** " 部分中，键入客户端应用程序注册名称。 如果找到该名称，则列出该应用程序的名称。 选择应用程序名称，然后选择 " **保存**"。 

如果找不到客户端应用程序，请检查应用程序注册，以确保名称正确。 确保在同一个租户中创建客户端应用程序，在该租户中，将在 Azure 医疗保健 Api (特此称为 FHIR service) 。


[![选择角色分配。 ](media/rbac/select-role-assignment.png) ](media/rbac/select-role-assignment.png#lightbox)

可以通过从 "**访问控制 (IAM)** " 菜单选项中选择 "**角色分配**" 选项卡来验证角色分配。
 

> [!NOTE]
> 角色分配可能需要几分钟才能传播到系统中。 如果无法在应用程序或其他测试工具中访问 FHIR 服务，可能需要等待几分钟。 另外，请检查是否已向应用程序注册中的 Azure 医疗保健 Api 授予了 user_impersonation 权限。

## <a name="next-steps"></a>后续步骤

本文介绍了如何为 FHIR 数据平面分配 Azure 角色。 若要了解如何使用 Postman 访问 FHIR 服务，请参阅

>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR 服务](../use-postman.md)