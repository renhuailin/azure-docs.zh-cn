---
title: 用于 MySQL 的 Azure 顾问
description: 了解用于 MySQL 的 Azure 顾问建议。
author: alau-ms
ms.author: alau
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 97f4e55b1d1888de00502d70c911f0bf24d31782
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653447"
---
# <a name="azure-advisor-for-mysql"></a>用于 MySQL 的 Azure 顾问

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解如何将 Azure 顾问应用于 Azure Database for MySQL，并获得常见问题的解答。
## <a name="what-is-azure-advisor-for-mysql"></a>什么是用于 MySQL 的 Azure 顾问？
Azure 顾问系统使用遥测为 MySQL 数据库发布性能和可靠性建议。 顾问建议在 MySQL 数据库产品/服务中进行拆分：
* Azure Database for MySQL - 单一服务器
* Azure Database for MySQL - 灵活服务器

某些建议通用于多种产品/服务，而其他建议基于特定于产品的优化。
## <a name="where-can-i-view-my-recommendations"></a>在哪里可以查看我的建议？
可以从 Azure 门户中的“概述”导航边栏中查看建议。 预览会以横幅通知的形式出现，用户可以在位于资源使用情况图正下方的“通知”部分中查看详细信息。

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="显示 Azure 顾问建议的 Azure 门户屏幕截图。":::

## <a name="recommendation-types"></a>建议类型
Azure Database for MySQL 优先考虑以下类型的建议：
* **性能**：提高 MySQL 服务器的运行速度。 其中包括 CPU 使用率、内存压力、连接池、磁盘利用率和特定于产品的服务器参数。 有关详细信息，请参阅[顾问性能建议](../advisor/advisor-performance-recommendations.md)。
* **可靠性**：确保并提高业务关键数据库的连续性。 其中包括存储限制和连接限制建议。 有关详细信息，请参阅[顾问可靠性建议](../advisor/advisor-high-availability-recommendations.md)。
* **成本**：优化并降低 Azure 总支出。 其中包括服务器适当调整大小的建议。 有关详细信息，请参阅[顾问成本建议](../advisor/advisor-cost-recommendations.md)。

## <a name="understanding-your-recommendations"></a>了解你的建议
* **每日计划**：对于 Azure MySQL 数据库，我们会检查服务器遥测数据，并按每日计划发布建议。 如果你对服务器配置进行更改，现有建议将保持可见，直到我们在第二天重新查看遥测数据。 
* **性能历史记录**：我们的一些建议基于性能历史记录。 这些建议只有在服务器以相同配置运行 7 天后才会出现。 这样一来，我们就可以检测持续的一段时间段内的大量使用情况（例如，高 CPU 活动或高连接量）的模式。 如果你预配新服务器或更改为新的 vCore 配置，这些建议将暂时停止。 这可防止旧遥测数据在重新配置的新服务器上触发建议。 但是，这也意味着可能不会立即识别基于性能历史记录的建议。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Azure 顾问概述](../advisor/advisor-overview.md)。
