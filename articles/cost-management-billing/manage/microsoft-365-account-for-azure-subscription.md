---
title: 使用 Microsoft 365 帐户注册 Azure
description: 了解如何使用 Microsoft 365 帐户创建 Azure 订阅。 还可以将现有的 Azure 和 Microsoft 365 帐户彼此关联。
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 26e2fec712d5ad249ffc548bed414b3d866894a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651249"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>使用 Microsoft 365 帐户注册 Azure 订阅

如果具有 Microsoft 365 订阅，可以使用 Microsoft 365 帐户创建 Azure 订阅。 使用 Microsoft 365 用户名和密码登录到 [Azure 门户](https://portal.azure.com/)。 如果想要设置虚拟机或使用其他 Azure 服务，则必须注册 Azure 订阅。 可以与他人共享 Azure 订阅并[使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理对 Azure 订阅和资源的访问](../../role-based-access-control/role-assignments-portal.md)。

如果已具有 Microsoft 365 帐户和 Azure 订阅，请参阅[将 Microsoft 365 租户与 Azure 订阅关联](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>使用 Microsoft 365 帐户获取 Azure 订阅

使用 Microsoft 365 用户名和密码注册 Azure 可节省时间并避免帐户激增。

1. 在 [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs) 上注册。
2. 使用 Microsoft 365 用户名和密码登录。 所使用的帐户无需具有管理员权限。 如果具有多个 Microsoft 365 帐户，请确保使用想要与 Azure 订阅关联的 Microsoft 365 帐户的凭据。

   ![显示登录页的屏幕截图。](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. 填写所需信息并完成注册过程。 如果已经具有 Microsoft 365 帐户，则可能不需要某些信息。

    ![显示注册表单的屏幕截图。](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- 如果需要将组织中的其他人员添加到 Azure 订阅，请参阅 [Azure 门户中的访问管理入门](../../role-based-access-control/overview.md)。

## <a name=""></a><a id="more-about-subs">有关 Azure 和 Microsoft 365 订阅的详细信息</a>

Microsoft 365 和 Azure 使用 Azure AD 服务管理用户和订阅。 Azure 目录类似于一个容器，可在其中对用户和订阅分组。 若要将相同用户帐户用于 Azure 和 Microsoft 365 订阅，需确保这些 Azure 订阅是在与 Microsoft 365 所在的相同目录中创建的。 请注意以下几点：

* 在目录下创建订阅
* 用户属于目录
* 订阅位于创建订阅的用户的目录中。 因此 Microsoft 365 订阅被捆绑到与 Azure 订阅相同的帐户。
* Azure 订阅归目录中的各个用户所有
* Microsoft 365 订阅归目录本身所有。 在目录中具有相应权限的用户可管理这些订阅。

![显示目录、用户和订阅关系的屏幕截图。](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

有关详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 与他人共享 Azure 订阅并[使用 Azure 基于角色的访问控制 (Azure RBAC) 来管理对 Azure 订阅和资源的访问](../../role-based-access-control/role-assignments-portal.md)。