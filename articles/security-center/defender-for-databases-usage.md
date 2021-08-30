---
title: 为开源关系数据库设置和响应来自 Azure Defender 的警报
description: 了解如何为开源关系数据库配置 Azure Defender 以检测异常的数据库活动，这些活动表示可能存在对数据库的安全威胁。
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1300a8ad11d7091085c95205b435a2d3fb077cd3
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369962"
---
# <a name="enable-azure-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>为开源关系数据库启用 Azure Defender 并响应警报

Azure Defender 会检测异常活动，这些活动表示存在对以下服务的数据库的异常和有害访问或攻击尝试：

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

若要从 Azure Defender 计划获取警报，首先需要按[如下所示](#enable-azure-defender)启用它。

请参阅[适用于开源关系数据库的 Azure Defender 的简介](defender-for-databases-introduction.md)，了解有关此 Azure Defender 计划的更多信息。

## <a name="enable-azure-defender"></a>启用 Azure Defender

1. 在 [Azure 门户](https://portal.azure.com)中，打开要保护的数据库服务器的配置页面。

1. 在左侧的安全菜单中，选择“安全中心”。

1. 如果未启用 Azure Defender，你将看到一个按钮，如下面的屏幕截图所示。 选择“启用适用于 [数据库类型] 的 Azure Defender”（例如，“适用于 MySQL 的 Azure Defender”），然后选择“保存” 。

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="启用适用于 MySQL 的 Azure Defender。" lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > 无论数据库类型是什么（PostgreSQL、MySQL 或 MariaDB），门户中的此页面都是相同的。

## <a name="respond-to-security-alerts"></a>响应安全警报

在数据库上启用 Azure Defender 后，它会检测异常活动并生成警报。 可从多个位置获取这些警报，包括：

- 在 Azure 门户中：
    - **Azure Defender 安全警报页** - 显示你有权查看的订阅中受 Azure Defender 保护的所有资源的警报。
    - 资源的 **安全中心** 页 - 显示针对一个特定资源的警报和建议，如上面[启用 Azure Defender](#enable-azure-defender) 中所示。
- 在组织中[指定接收电子邮件警报的人员](security-center-provide-security-contact-details.md)的收件箱中。  

> [!TIP]
> [Azure 安全中心概述仪表板](overview-page.md)上的实时磁贴跟踪有关所有资源（包括数据库）的活动威胁的状态。 选择磁贴以启动 Azure Defender 警报页，概要了解在数据库中检测到的活动威胁。
>
> 有关响应 Azure Defender 警报的详细步骤和建议方法，请参阅[响应安全警报](tutorial-security-incident.md#respond-to-a-security-alert)。


### <a name="respond-to-email-notifications-of-security-alerts"></a>响应安全警报的电子邮件通知

Azure Defender 在检测到异常数据库活动时会发送电子邮件通知。 该电子邮件包含可疑安全事件的详细信息，例如异常活动的性质、数据库名称、服务器名称、应用程序名称和事件时间。 该电子邮件还会提供有关可能原因和建议措施的信息，帮助调查和缓解对数据库的任何潜在威胁。

1. 选择电子邮件中的“查看完整警报”链接，以启动 Azure 门户并显示警报页面，概要了解在数据库中检测到的活动威胁。
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="Azure Defender 关于可疑的暴力攻击的电子邮件通知。":::

    从安全中心门户页面查看订阅级别的活动威胁：

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="Azure 安全中心显示一个或多个订阅上的活动威胁。" lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. 可选择特定警报，了解有关调查当前威胁和修正未来威胁的其他详细信息和建议操作。
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="特定警报的详细信息。" lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> 有关如何处理警报的详细教程，请参阅[教程：安全警报的会审、调查和响应](tutorial-security-incident.md)。


## <a name="next-steps"></a>后续步骤

- [自动执行对安全中心触发器的响应](workflow-automation.md)
- [将警报流式传输到 SIEM、SOAR 或 ITSM 解决方案](export-to-siem.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)