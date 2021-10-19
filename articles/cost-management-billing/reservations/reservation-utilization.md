---
title: 查看 Azure 预留项利用率
description: 了解如何获取预留项利用率和详细信息。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 15726451525d3ffcaf2d059b9e6c9dee86058591
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710201"
---
# <a name="view-reservation-utilization-after-purchase"></a>查看购买后的预留项利用率

你可以在 Azure 门户和成本管理 Power BI 应用中查看预留项利用率百分比和使用预留项的资源。

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>使用 Azure RBAC 访问权限在 Azure 门户中查看利用率

若要查看预留利用率，必须拥有对预留项的 Azure RBAC 访问权限，或者拥有可管理所有 Azure 订阅和管理组的提升的访问权限。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到[预留项](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
1. 该列表显示你对其具有“所有者”或“读者”角色的所有预留。 每个预留显示最后已知的利用率百分比。
1. 选择利用率百分比查看使用历史记录和详细信息。 下面的视频显示了一个示例。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>以计费管理员身份查看利用率

企业协议 (EA) 管理员或 Microsoft 客户协议 (MCA) 计费管理员可以从“成本管理 + 计费”中查看利用率。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到“成本管理 + 计费” > “预留项” 。
1. 选择利用率百分比查看使用历史记录和详细信息。

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>使用 API、PowerShell 和 CLI 获取预留项和利用率

可以使用预留实例使用情况 API 获取[预留项利用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。

## <a name="see-reservations-and-utilization-in-power-bi"></a>在 Power BI 中查看预留项和利用率

Power BI 用户可使用两个选项：

- 适用于 Power BI Desktop 的成本管理连接器 - 预留项购买日期和利用率数据在[适用于 Power BI Desktop 的成本管理连接器](/power-bi/desktop-connect-azure-cost-management)中提供。 可使用此连接器创建所需的报表。
- 成本管理 Power BI 应用 - 使用[成本管理 Power BI 应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)获取可进一步自定义的预创建报表。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)。
- [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)。
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)。
- [了解 CSP 订阅的预留使用情况](/partner-center/azure-reservations)。
