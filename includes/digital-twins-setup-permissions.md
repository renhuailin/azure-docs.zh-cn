---
author: baanders
description: Azure 数字孪生安装程序中的权限必备组件包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205468"
---
## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

若要完成本文中的所有步骤，需要 [在订阅中](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 拥有具有以下权限的角色：
* 创建和管理 Azure 资源
* 管理用户对 Azure 资源的访问权限， (包括授予和委派权限) 

满足此要求的常见角色包括 " *所有者*"、" *帐户管理员*" 或 " *用户访问管理员* " 和 " *参与者*" 的组合。 有关角色和权限的完整说明，包括其他角色包含哪些权限，请访问 Azure RBAC 文档中的 [*经典订阅管理员角色、Azure 角色和 Azure AD 角色*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) 。

若要查看你在订阅中的角色，请访问 Azure 门户中的 " [订阅" 页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) (你可以使用此链接或通过门户搜索栏) 查找 *订阅* 。 查找正在使用的订阅的名称，并在 " *我的角色* " 列中查看其角色：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Azure 门户中的 &quot;订阅&quot; 页的视图，将用户显示为所有者" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

如果发现值为 " *参与者*" 或另一个没有上述所需权限的角色，则可以联系订阅上具有这些权限的用户 (例如 *，订阅* 所有者或帐户管理员) ，并按以下方式之一进行操作：
* 请求用户以你的名义完成本文中的步骤
* 请求它们提升您在订阅上的角色，以便您有权自行进行。 这是否合适取决于你的组织和其中的角色。