---
title: 管理对 Azure 计费的访问
description: 了解如何向团队成员授予访问 Azure 计费信息的权限。
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/27/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 2aae9775c884f8ccedc1f3ce70025c5331030133
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112987844"
---
# <a name="manage-access-to-billing-information-for-azure"></a>管理对 Azure 账单信息的访问权限

你可以在 Azure 门户中向其他人提供对你帐户的账单信息的访问权限。 计费角色的类型以及提供计费信息访问权限的说明因计费帐户的类型而异。 若要确定计费帐户的类型，请参阅[检查计费帐户的类型](#check-the-type-of-your-billing-account)。

本文适用于使用 Microsoft 联机服务程序帐户的客户。 如果你是签订了企业协议 (EA) 的 Azure 客户，并且是企业管理员，则可在 Enterprise Portal 中将权限授予部门管理员和帐户所有者。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。 如果你是 Microsoft 客户协议客户，请参阅[了解 Azure 中的 Microsoft 客户协议管理角色](understand-mca-roles.md)。

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Microsoft 联机服务程序帐户的帐户管理员

帐户管理员是 Microsoft 联机服务程序计费帐户的唯一所有者。 该角色将分配给已注册 Azure 的人员。 帐户管理员有权执行各种计费任务，例如创建订阅、查看发票或更改订阅的账单。

## <a name="give-others-access-to-view-billing-information"></a>向他人授予查看账单信息的访问权限

帐户管理员可以通过在其帐户中为订阅分配以下角色之一来授予其他人访问 Azure 账单信息的权限。

- 服务管理员
- 共同管理员
- 所有者
- 参与者
- 读者
- 计费读者

这些角色可以在 [Azure 门户](https://portal.azure.com/)中访问账单信息。 分配了这些角色的人员也可以使用[计费 API](consumption-api-overview.md#usage-details-api) 以编程方式获取发票和使用详细信息。

若要分配角色，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

> [!note]
> 如果你是 EA 客户，帐户所有者可以将上述角色分配给其团队的其他用户。 但是，要让这些用户查看账单信息，企业管理员必须在 Enterprise Portal 中启用 AO 查看费用。


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> 允许用户下载发票

帐户管理员将适当角色分配给其他用户后，他们必须在 Azure 门户中打开下载发票的访问权限。 早于 2016 年 12 月的发票仅供帐户管理员查看。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com/)，

1. 在“成本管理 + 计费”中进行搜索。

    ![突出显示“服务”部分下的“成本管理 + 计费”的屏幕截图。](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. 在左侧窗格中选择“订阅”。 根据你的访问权限，可能需要选择计费范围，然后选择“订阅”。

    ![显示选择订阅的屏幕截图。](./media/manage-billing-access/billing-select-subscriptions.png)

1. 选择“发票”，然后选择“访问发票”。

    ![显示如何委托对发票的访问权限的屏幕截图。](./media/manage-billing-access/aa-optin01.png)

1. 选择“启用”，然后进行保存。

    ![屏幕截图，显示用于委托对发票的访问权限的开关](./media/manage-billing-access/aa-optinallow01.png)

帐户管理员还可配置通过电子邮件发送发票。 若要了解详细信息，请参阅[在电子邮件中获取发票](download-azure-invoice-daily-usage-date.md)。

## <a name="give-read-only-access-to-billing"></a>授予对账单信息的只读访问权限

将“账单读取者”角色分配给需要以只读方式访问订阅账单信息的人员，而不是需要 Azure 服务管理或创建功能的人员。 此角色适用于组织中负责 Azure 订阅的财务和成本管理的用户。

计费读取者功能处于预览状态，尚不支持非全局云。

- 将“计费读者”角色分配给订阅范围内的用户。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

> [!NOTE]
> 如果你是 EA 客户，则帐户所有者或部门管理员可以将“账单读取者”角色分配给团队成员。 但是，企业管理员必须在企业门户中启用“AO 视图费用”或“DA 视图费用”策略，该账单读取者才能查看部门或帐户的账单信息。

## <a name="check-the-type-of-your-billing-account"></a>检查计费帐户的类型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 其他角色的用户（如所有者或参与者）不仅可访问计费信息，还可访问 Azure 服务。 若要管理这些角色，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
- 有关角色的详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。