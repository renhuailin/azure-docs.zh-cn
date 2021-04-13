---
author: baanders
description: Azure 数字孪生安装程序中的权限先决条件的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92205468"
---
## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求

为了能够完成本文中的所有步骤，你需要[在订阅中有一个具有以下权限的角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)：
* 创建和管理 Azure 资源
* 管理用户对 Azure 资源的访问权限（包括授予和委托权限）

满足此要求的常见角色包括“所有者”、“帐户管理员”或“用户访问管理员”和“参与者”的组合。 有关角色和权限的完整说明（包括其他角色包含哪些权限），请访问 Azure RBAC 文档中的[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。

若要查看你在订阅中的角色，请访问 Azure 门户中的[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)（你可以使用此链接，也可以通过门户搜索栏查找“订阅”）。 查找你正在使用的订阅的名称，然后在“我的角色”列中查看你在该订阅中的角色：

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Azure 门户中“订阅”页的视图，显示用户为所有者" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

如果你发现值是“参与者”，或者其他没有上述所需权限的角色，则可以联系订阅上有这些权限的用户（例如订阅所有者或帐户管理员），并按以下方式之一继续操作：
* 请求他们以你的名义完成本文中的步骤
* 请求他们提升你在订阅中的角色，以便你有权自行继续执行。 这是否合适取决于你的组织和你在其中的角色。