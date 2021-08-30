---
title: Azure Database for MySQL 单一服务器中的新增内容
description: 了解 Azure Database for MySQL 单一服务器的最近更新，这是 Microsoft 云中基于 MySQL 社区版的关系数据库服务。
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: b324c6b536ae5038ed9eb1d31025d6e88cf21011
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779678"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL 单一服务器中有哪些新增内容？

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

在 Microsoft 云中，Azure Database for MySQL 是关系数据库服务。 该服务基于 [MySQL 社区版](https://www.mysql.com/products/community/)（可在 GPLv2 许可下使用）数据库引擎，并支持版本 5.6、5.7 和 8.0。 [Azure Database for MySQL 单一服务器](./overview.md#azure-database-for-mysql---single-server)是一种部署模式，可提供完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性和安全性。

本文总结了自 2021 年 1 月开始的 Azure Database for MySQL 单一服务器中的新版本和功能。 列表按时间倒序排列，最新更新排在最前。

## <a name="june-2021"></a>2021 年 6 月
  
此版本的 Azure Database for MySQL 单一服务器包括以下更新。

- **已实现从 MySQL 8.0 的门户/CLI 更改服务器参数 `activate_all_roles_on_login` 的功能**

  用户现在可以使用 Azure 门户和 CLI 更改 activate_all_roles_on_login 参数的值。 此参数有助于配置在用户登录服务器时是否启用所有已授权角色的自动激活。 有关详细信息，请参阅[服务器系统变量](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)。

- **解决了 MySQL 社区版 Bug #29596969 和 #94668**

  此版本解决了 MySQL 8.0 中当字段被标记为 PRIMARY KEY 时，CREATE TABLE 查询会忽略默认表达式的问题。 （MySQL 社区版 Bug #29596969、Bug #94668）。 有关详细信息，请参阅 [MySQL Bug：#94668：当字段被标记为 PK 时，默认表达式在 CREATE TABLE 查询期间为 NULL](https://bugs.mysql.com/bug.php?id=94668)

- **解决了“SHOW TABLE”查询中表名称重复的问题**

  我们引入了一个新函数，用于在表操作期间对表缓存进行精细控制。 由于新功能中存在代码缺陷，因此可能会错误配置或添加目录缓存中的条目，并导致出现意外行为（例如返回两个名称相同的表）。 目录缓存仅适用于“SHOW TABLE”相关查询；它不会影响任何 DML 或 DDL 查询。 在此版本中已彻底修复了此问题。

- **增加了服务器参数 `max_heap_table_size` 的默认值，以帮助减少临时表溢出到磁盘的情况**

  在此版本中，对于常规用途 64 vCore 和内存优化 32 vCore，参数 `max_heap_table_size` 的最大允许值已更改为 8589934592。

- **解决了在门户中设置参数 `sql_require_primary_key` 的值时遇到的问题**

  用户现在可以直接从 Azure 门户修改参数 `sql_require_primary_key` 的值。

- **计划内维护通知的正式发布**

  此版本在 Azure Database for MySQL - 单一服务器中提供计划内维护通知的正式发布。 有关详细信息，请参阅[计划内维护通知](concepts-planned-maintenance-notification.md)一文。

- **默认启用了参数 `redirect_enabled`**

  在此版本中，将默认启用参数 `redirect_enabled`。 重定向是为了减小客户端应用程序与 MySQL 服务器之间的网络延迟，因为它允许应用程序直接连接到后端服务器节点。 PHP 应用程序中的重定向支持是通过 Microsoft 开发的 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展提供的。 有关详细信息，请参阅[通过重定向连接到 Azure Database for MySQL](howto-redirection.md) 一文。

>[!Note]
> * 重定向不适用于专用链接设置。 如果使用的是 Azure Database for MySQL 的专用链接，则可能会遇到连接问题。 若要解决此问题，请确保参数 redirect_enabled 设置为“关闭”，然后重启客户端应用程序。</br>
> * 如果具有使用 mysqlnd_azure 重定向驱动程序连接到 Azure Database for MySQL 的 PHP 应用程序，则可能遇到影响插入事务的数据编码问题。</br>
> 若要解决此问题，请执行以下操作之一：
>    - 在 Azure 门户中，通过将 redirect_enabled 参数设置为“关闭”来禁用重定向，然后重启 PHP 应用程序以在更改后清除驱动程序缓存。
>     - 建立连接后，根据设置在会话级别显式设置字符集相关参数（例如“设置名称 utf8mb4”）。

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

- 了解有关 [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/mysql/server/)的详细信息。
- 浏览 Azure Database for MySQL 单一服务器的[公共文档](./single-server/index.yml)。
- 查看有关[排查常见错误](./howto-troubleshoot-common-errors.md)的详细信息。