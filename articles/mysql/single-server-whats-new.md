---
title: Azure Database for MySQL 单一服务器中的新增内容
description: 了解 Azure Database for MySQL 单一服务器的最近更新，这是 Microsoft 云中基于 MySQL 社区版的关系数据库服务。
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 6b77ede348073dd09c0ba44bfe282d3bf546a092
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958542"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL 单一服务器中有哪些新增内容？

在 Microsoft 云中，Azure Database for MySQL 是关系数据库服务。 该服务基于 [MySQL 社区版](https://www.mysql.com/products/community/)（可在 GPLv2 许可下使用）数据库引擎，并支持版本 5.6、5.7 和 8.0。 [Azure Database for MySQL 单一服务器](./overview.md#azure-database-for-mysql---single-server)是一种部署模式，可提供完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性和安全性。

本文总结了自 2021 年 1 月开始的 Azure Database for MySQL 单一服务器中的新版本和功能。 列表按时间倒序显示，最新的更新排在最前。

## <a name="february-2021"></a>2021 年 2 月

此版本的 Azure Database for MySQL 单一服务器包括以下更新。

- 添加了新的存储过程，以支持 5.7 和 8.0 版大型存储服务器的用于数据输入的全局事务标识符 (GTID)。
- 已更新以支持 MySQL 版本 5.6.50 和 5.7.32。

## <a name="january-2021"></a>2021 年 1 月

此版本的 Azure Database for MySQL 单一服务器包括以下更新。

- 启用了“重置密码”以自动修复第一个管理员权限。
- 公开了 `auto_increment_increment/auto_increment_offset` 服务器参数和 `session_track_gtids`。
- 添加了用于控制 innodb 缓冲池转储/还原的新存储过程。
- 公开了大型存储服务器的 innodb 预热相关服务器参数。

## <a name="contacts"></a>联系人

如果对使用 Azure Database for MySQL 有任何疑问或建议，请联系 Azure Database for MySQL 团队 ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))。 此电子邮件地址并不是技术支持别名。

另外，请酌情考虑以下联系点：

- 若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 创建条目。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/mysql/server/)。
- 浏览 Azure Database for MySQL 单一服务器的[公共文档](./single-server/index.yml)。
- 查看有关[排查常见错误](./howto-troubleshoot-common-errors.md)的详细信息。