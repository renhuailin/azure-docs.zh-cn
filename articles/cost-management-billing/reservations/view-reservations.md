---
title: 查看 Azure 资源的预留
description: 了解如何在 Azure 门户中查看 Azure 预留。 使用 API、PowerShell、CLI 和 Power BI 查看预留项和利用率。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561098"
---
# <a name="view-azure-reservations"></a>查看 Azure 预留项

本文介绍如何在 Azure 门户中查看 Azure 预留。 可以在 Azure 门户中查看和管理购买的预留项。

## <a name="who-can-manage-a-reservation-by-default"></a>默认情况下谁可以管理预留

默认情况下，以下用户可以查看和管理预留：

- 预留订单中将添加购买预留的人员和用于购买预留的计费订阅的帐户管理员。
- 企业协议和 Microsoft 客户协议计费管理员。

若要允许其他人管理预留，可通过两种方式实现：

- 委托单个预留订单的访问管理：
    1. 登录 [Azure 门户](https://portal.azure.com)。
    1. 选择“所有服务” > “预订”以列出自己有权访问的预订。
    1. 选择要将访问权限委托给其他用户的预订。
    1. 从预留详细信息中，选择预留订单。
    1. 选择“访问控制 (IAM)”。
    1. 选择“添加角色分配” > “角色” > “所有者”。 如果希望提供有限的访问权限，请选择其他角色。
    1. 键入要添加为“所有者”的用户的电子邮件地址。
    1. 选择用户，再选择“保存”。

- 将用户作为计费管理员添加到企业协议或 Microsoft 客户协议：
    - 对于企业协议，请添加具有“企业管理员”角色的用户，以便查看和管理适用于企业协议的所有预留订单。 具有“企业管理员（只读）”角色的用户可以查看预留。 除非使用访问控制 (IAM) 将部门管理员和帐户所有者显式添加到预留中，否则他们无法查看相关预留。 有关详细信息，请参阅[管理 Azure 企业角色](../manage/understand-ea-roles.md)。

        企业管理员可以获得预留订单的所有权，并可以使用访问控制 (IAM) 将其他用户添加到预留。
    - 在 Microsoft 客户协议下，具有计费配置文件所有者角色或计费配置文件参与者角色的用户可以管理使用计费配置文件完成的所有预留购买。 计费配置文件阅读者和发票管理员可以通过计费配置文件查看付费的所有预留。 但是，他们不能对预留进行更改。
    有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="how-billing-administrators-view-or-manage-reservations"></a>计费管理员如何查看或管理预留

1. 请转到“成本管理 + 计费”，然后在页面左侧选择“预留事务” 。
2. 如果具有所需的计费权限，便可以查看和管理预留。 如果看不到任何预留，请确保使用创建预留的 Azure AD 租户登录。

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>在 Azure 门户中查看预留和利用率

若要以所有者或读取者身份查看预留，请执行以下操作

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 转到[预留项](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
3. 该列表显示你对其具有“所有者”或“读者”角色的所有预留。 每个预留显示最后已知的利用率百分比。
4. 选择利用率百分比查看使用历史记录和详细信息。 请查看以下视频中的详细信息。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>使用 API、PowerShell 和 CLI 获取预留项和利用率

使用以下资源获取所有预留项的列表：

- [API：预留订单 - 列表](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell：预留订单 - 列表](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI：预留订单 - 列表](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

还可以使用预留实例使用情况 API 获取[预留项利用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。 

## <a name="see-reservations-and-utilization-in-power-bi"></a>在 Power BI 中查看预留项和利用率

Power BI 用户可使用两个选项
- 内容包：预留项购买数据和利用率数据在 [Consumption Insights Power BI 内容包](/power-bi/desktop-connect-azure-cost-management)中提供。 可使用此内容包创建所需的报表。 
- 成本管理应用：使用[成本管理应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)获取可以进一步自定义的预创建报表。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)。
- [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)。
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)。
- [了解 CSP 订阅的预留使用情况](/partner-center/azure-reservations)。

