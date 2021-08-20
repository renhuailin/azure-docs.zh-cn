---
title: 用于开源关系数据库的 Azure Defender - 优势和功能
description: 了解用于开源关系数据库（例如 PostgreSQL、MySQL 和 MariaDB）的 Azure Defender 的优势和功能
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: aa02c405a3d94426e54ed7e3499f6ae79ec635e5
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113487320"
---
# <a name="introduction-to-azure-defender-for-open-source-relational-databases"></a>用于开源关系数据库的 Azure Defender 的简介

此 Azure Defender 计划为以下开源关系数据库提供威胁防护：

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Azure Defender 会检测异常活动，这些活动表示可能有害的异常数据库访问或攻击尝试。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用该计划轻松解决数据库的潜在威胁。

## <a name="availability"></a>可用性

| 方面                             | 详细信息                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                     | 正式发布版 (GA)                                                     |
| 定价：                           | 用于开源关系数据库的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中所示的定价计费   |
| PostgreSQL 的受保护版本：  | 单一服务器 -“常规用途”和“内存优化”。 若要了解详细信息，请查看 [PostgreSQL 定价层](../postgresql/concepts-pricing-tiers.md)。   |
| MySQL 的受保护版本：       | 单一服务器 -“常规用途”和“内存优化”。 若要了解详细信息，请查看 [MySQL 定价层](../mysql/concepts-pricing-tiers.md)。                        |
| MariaDB 的受保护版本：     | “常规用途”和“内存优化”。 若要了解详细信息，请查看 [MariaDB 定价层](../mariadb/concepts-pricing-tiers.md)。                      |
| 云：                            | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/no-icon.png":::国家/地区/主权（US Gov，Azure 中国） |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-azure-defender-for-open-source-relational-databases"></a>用于开源关系数据库的 Azure Defender 有哪些优势？

Azure Defender 提供针对异常活动的安全警报，以便检测潜在威胁，并在发生威胁时做出响应。

启用此计划时，Azure Defender 将在检测到异常的数据库访问和查询模式以及可疑的数据库活动时发出警报。

这些警报显示在 Azure Defender 的“安全警报”页面中，并包括：

- 触发警报的可疑活动的详细信息
- 关联的 MITRE ATT&CK 技巧
- 有关如何调查和缓解威胁的建议操作
- 继续使用 Azure Sentinel 进行调查的选项

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="受用于开源关系数据库的 Azure Defender 保护的数据库可能出现的部分安全警报。" lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-azure-defender-for-open-source-relational-databases-provide"></a>用于开源关系数据库的 Azure Defender 提供什么类型的警报？

存在以下情况时，会触发具有大量威胁情报的安全警报：

- **异常的数据库访问和查询模式** - 例如，使用不同的凭据尝试登录，但登录失败的次数异常多（暴力破解尝试）
- **可疑的数据库活动** - 例如，合法用户从遭到入侵的计算机访问 SQL Server，而此计算机曾与加密挖掘 C&C 服务器通信
- **暴力攻击** - 能够将简单的暴力攻击与有效用户的暴力攻击或成功的暴力攻击区分开来

> [!TIP]
> 若要查看数据库服务器的安全警报的完整列表，请查看[警报参考页](alerts-reference.md#alerts-osrdb)。



## <a name="next-steps"></a>后续步骤

本文介绍了用于开源关系数据库的 Azure Defender。

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)
