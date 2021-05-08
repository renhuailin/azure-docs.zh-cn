---
title: 如何手动修复 ServiceNow 同步问题
description: 重置与 ServiceNow 的连接，以便 Microsoft Azure 中的警报可以再次调用 ServiceNow
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 664f1522775d96b813b7cd99bdffdb26630497f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037432"
---
# <a name="how-to-manually-fix-sync-problems"></a>如何手动修复同步问题

Azure Monitor 可以连接到第三方 IT 服务管理 (ITSM) 提供程序。 ServiceNow 是这些提供程序之一。

出于安全原因，你可能需要刷新用于与 ServiceNow 连接的身份验证令牌。
使用以下同步过程重新激活连接并刷新令牌：

1. 在顶部搜索横幅中搜索解决方案，然后选择相关解决方案

    ![显示顶部搜索横幅和在何处选择相关解决方案的屏幕截图。](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. 在“解决方案”屏幕的订阅筛选器中选择“全选”，然后按“ServiceDesk”筛选

    ![显示在何处选择“全选”和按“ServiceDesk”筛选的屏幕截图。](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. 选择 ITSM 连接的解决方案。
1. 在左侧横幅中选择“ITSM 连接”。

    ![显示在何处选择“ITSM 连接”的屏幕截图。](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. 从列表中选择每个连接器。 
    1. 单击连接器名称以对其进行配置
    1. 删除任何不再使用的连接器

    1. 基于合作伙伴类型并根据[定义](./itsmc-connections.md)更新字段

    1. 单击“同步”

       ![突出显示“同步”按钮的屏幕截图。](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. 单击“保存”

        ![新建连接](media/itsmc-resync-servicenow/save-8-bit.png)

f.    查看通知以确认是否已启动该过程。
