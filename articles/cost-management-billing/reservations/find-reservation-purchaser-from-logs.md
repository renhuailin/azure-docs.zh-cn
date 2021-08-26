---
title: 从 Azure Monitor 日志中查找预留项购买者
description: 本文通过 Azure Monitor 日志中的信息帮助查找预留项购买者。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: baf7fe1968f2ba2a72e27f1caf85d02c65a3e93f
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514753"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>从 Azure 日志中查找预留项购买者

本文通过目录日志中的信息帮助查找预留项购买者。 Azure Monitor 的目录日志显示了购买预留项的用户的电子邮件 ID。

## <a name="find-the-purchaser"></a>查找购买者

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到“Monitor” > “活动日志” > “活动”  。  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="显示导航到“活动日志”-“活动”的屏幕截图。" lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. 选择“目录活动”。 如果你看到一条消息，显示“你需要权限才能查看目录级别日志”，请选择[链接](../../role-based-access-control/elevate-access-global-admin.md)以了解如何获取权限。  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="显示“目录活动”的屏幕截图，其中显示没有查看日志的权限。" lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. 获得权限后，请使用 Microsoft.Capacity 筛选“租户资源提供程序” 。 你应会看到所选时间范围内的所有预留项相关事件。 请根据更改时间跨度。  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="显示已购买预留项的用户的屏幕截图。" lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    如有必要，你可能需要选择“编辑列”，以选择“事件发起者” 。
   已购买预留项的用户会显示在“事件发起者”下方。

## <a name="next-steps"></a>后续步骤

- 如有需要，计费管理员可以[获得预留项的所有权](view-reservations.md#view-and-manage-reservations)。
