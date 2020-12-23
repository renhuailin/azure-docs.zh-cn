---
title: '版本控制策略-Azure Database for MySQL-单服务器和灵活的服务器 (预览版) '
description: 描述围绕 MySQL 主要版本和次要版本的策略 Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 4903f1e48eb2f33c68d62c635201474b841ed146
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591506"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Azure Database for MySQL 版本控制策略

本页介绍 Azure Database for MySQL 版本控制策略，适用于 Azure Database for MySQL 单一服务器和 Azure Database for MySQL 灵活的服务器 (预览版) 部署模式。

## <a name="supported--mysql-versions"></a>支持的 MySQL 版本

Azure Database for MySQL 支持以下数据库版本。

| Version | 单台服务器 | 灵活服务器（预览版） |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5。7 | X | X |
| MySQL 5。6| X |  |


## <a name="major-version-support"></a>主要版本支持
将支持 MySQL 的每个主要版本，Azure Database for MySQL 从 Azure 开始支持版本到版本被 MySQL 社区淘汰，如 [版本控制策略](https://www.mysql.com/support/eol-notice.html)中所述。

## <a name="minor-version-support"></a>次版本支持
在定期维护过程中，Azure Database for MySQL 会自动执行到 Azure 首选 MySQL 版本的次要版本升级。 

## <a name="major-version-retirement-policy"></a>主要版本停用策略
下表提供了 MySQL 主要版本的停用详细信息。 这些日期遵循 [MySQL 版本控制策略](https://www.mysql.com/support/eol-notice.html)。

| Version | 新增功能 | Azure 支持开始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| [MySQL 5。6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [功能](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018 年 3 月 20 日 | 2021年2月
| [MySQL 5。7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018 年 3 月 20 日 | 2023年10月
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html))  | 2019 年 12 月 11 日 | 2026年4月


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Azure Database for MySQL 中不支持停用的 MySQL 引擎版本

在每个 MySQL 数据库版本的停用日期之后，如果继续运行停用的版本，请注意以下限制：
- 由于社区不会发布任何更多的 bug 修复程序或安全修补程序，Azure Database for MySQL 将不会修补已停用的数据库引擎的任何错误或安全问题，或者采用与停用的数据库引擎有关的安全措施。 但是，Azure 将继续针对主机、OS、容器和任何其他与服务相关的组件执行定期维护和修补。
- 如果你可能会遇到与 MySQL 数据库相关的任何支持问题，我们可能无法为你提供支持。 在这种情况下，你将必须升级数据库，以便我们为你提供任何支持。
- 你将不能为已停用的版本创建新的数据库服务器。 但是，你将能够执行时间点恢复，并为现有服务器创建读取副本。
- Azure Database for MySQL 开发的新服务功能仅适用于受支持的数据库服务器版本。
- 运行时间 Sla 仅适用于与 Azure Database for MySQL 服务相关的问题，不适用于与数据库引擎相关的错误引起的任何停机时间。  
- 在对由停用的数据库版本中的 MySQL 数据库引擎漏洞导致的服务产生严重威胁的极端情况下，Azure 可能会选择停止数据库服务器的计算节点，以首先保护服务。 系统将要求你在使服务器联机之前升级服务器。 在升级过程中，你的数据将始终使用在服务上执行的自动备份进行保护，如果需要，可用于还原到较旧版本。 



## <a name="next-steps"></a>后续步骤
- 请参阅 Azure Database for MySQL-单一服务器 [支持的版本](./concepts-supported-versions.md)
- 请参阅 Azure Database for MySQL-灵活的服务器 (预览版) [支持的版本](flexible-server/concepts-supported-versions.md)
- 若要执行升级，请参阅 MySQL [转储和还原](./concepts-migrate-dump-restore.md) 。
