---
title: 查看和管理 Azure 预留项的权限
description: 了解如何在 Azure 门户中查看和管理 Azure 预留项。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: d7bb27e511d799a0afe203b8147546647cc658fd
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989068"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>查看和管理 Azure 预留项的权限

本文介绍了预留权限的工作原理以及用户如何在 Azure 门户中查看和管理 Azure 预留项。

## <a name="who-can-manage-a-reservation-by-default"></a>默认情况下谁可以管理预留

默认情况下，以下用户可以查看和管理预留：

- 预留订单中将添加购买预留的人员和用于购买预留的计费订阅的帐户管理员。
- 企业协议和 Microsoft 客户协议计费管理员。
- 拥有可管理所有 Azure 订阅和管理组的提升访问权限的用户

预留项的生命周期独立于 Azure 订阅，因此预留项不是 Azure 订阅下的资源。 相反，它是一个租户级资源，具有自己的 Azure RBAC 权限，与订阅无关。 在购买后，预留项不会从订阅继承权限。

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>计费管理员如何查看或管理预留项

如果你是计费管理员，请使用以下步骤来查看和管理所有预留项与预留交易。

1. 登录到 [Azure 门户](https://portal.azure.com)，导航到“成本管理 + 计费”。
    - 如果你是 EA 管理员，请在左侧菜单中选择“计费范围”，然后在计费范围列表中选择一个范围。
    - 如果你是 Microsoft 客户协议计费配置文件所有者，请在左侧菜单中选择“计费配置文件”。 在计费配置文件列表中选择一个计费配置文件。
1. 在左侧菜单中，选择“产品 + 服务” > “预留项” 。
1. 此时将显示 EA 注册或计费配置文件的完整预留项列表。
1. 计费管理员可以通过选择预留项，然后在出现的窗口中选择“授予访问权限”，来取得预留项的所有权。

### <a name="how-to-add-billing-administrators"></a>如何添加计费管理员

将用户作为计费管理员添加到企业协议或 Microsoft 客户协议：

- 对于企业协议，请添加具有“企业管理员”角色的用户，以便查看和管理适用于企业协议的所有预留订单。 企业管理员可以在“成本管理 + 计费”中查看和管理预留项。
    - 具有“企业管理员（只读）”角色的用户只能从“成本管理 + 计费”中查看预留项。 
    - 除非使用访问控制 (IAM) 将部门管理员和帐户所有者显式添加到预留中，否则他们无法查看相关预留。 有关详细信息，请参阅[管理 Azure 企业角色](../manage/understand-ea-roles.md)。
- 在 Microsoft 客户协议下，具有计费配置文件所有者角色或计费配置文件参与者角色的用户可以管理使用计费配置文件完成的所有预留购买。 计费配置文件阅读者和发票管理员可以通过计费配置文件查看付费的所有预留。 但是，他们不能对预留进行更改。
    有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="view-reservations-with-azure-rbac-access"></a>使用 Azure RBAC 访问权限查看预留项

如果你购买了预留项或已添加到预留项，请使用以下步骤来查看和管理预留项。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务” > “预订”以列出自己有权访问的预订。

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>拥有提升访问权限的用户可管理所有 Azure 订阅和管理组

你可以提升用户的[访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)。

拥有提升的访问权限后：

1. 导航到“所有服务” > “预留项”以查看租户中的所有预留项 。
1. 若要对预留项进行修改，请使用访问控制 (IAM) 将自己添加为预留订单的所有者。

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>向用户授予单个预留项的 Azure RBAC 权限

对预留项具有所有者访问权限的用户和计费管理员可以为单个预留订单委派访问管理。

若要允许其他人管理预留，可通过两种方式实现：

- 通过向单个预留订单的资源范围内的用户分配“所有者”角色，委托该预留订单的访问权限管理。 如果希望提供有限的访问权限，请选择其他角色。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

- 将用户作为计费管理员添加到企业协议或 Microsoft 客户协议：
    - 对于企业协议，请添加具有“企业管理员”角色的用户，以便查看和管理适用于企业协议的所有预留订单。 具有“企业管理员（只读）”角色的用户可以查看预留。 除非使用访问控制 (IAM) 将部门管理员和帐户所有者显式添加到预留中，否则他们无法查看相关预留。 有关详细信息，请参阅[管理 Azure 企业角色](../manage/understand-ea-roles.md)。

        企业管理员可以获得预留订单的所有权，并可以使用访问控制 (IAM) 将其他用户添加到预留。
    - 在 Microsoft 客户协议下，具有计费配置文件所有者角色或计费配置文件参与者角色的用户可以管理使用计费配置文件完成的所有预留购买。 计费配置文件阅读者和发票管理员可以通过计费配置文件查看付费的所有预留。 但是，他们不能对预留进行更改。
    有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)。