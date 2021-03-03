---
title: 版本控制策略 - Azure Database for MySQL 单一服务器和灵活服务器（预览版）
description: 描述有关 Azure Database for MySQL 中 MySQL 主要版本和次要版本的策略
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 6acb3268ba40399612940b395437fde3beffda1a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732864"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Azure Database for MySQL 版本控制策略

本页介绍 Azure Database for MySQL 版本控制策略，适用于 Azure Database for MySQL 单一服务器和 Azure Database for MySQL 灵活的服务器 (预览版) 部署模式。

## <a name="supported--mysql-versions"></a>支持的 MySQL 版本

已使用 InnoDB 存储引擎从 [MySQL 社区版](https://www.mysql.com/products/community/)开发 Azure Database for MySQL。 服务支持社区支持的所有当前主要版本，即 MySQL 5.6、5.7 和8.0。 MySQL 使用 X.x.x.x 命名方案，其中 X 为主要版本，Y 为次版本，Z 为 bug 修复版本。 有关方案的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

> [!NOTE]
> 在单服务器部署选项中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。

Azure Database for MySQL 当前支持以下主要和次要版本的 MySQL：

| 版本 | 单台服务器 <br/> 当前次要版本 |灵活服务器（预览版） <br/> 当前次要版本  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 版本 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) | 不支持|
|MySQL 版本 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 版本 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|


## <a name="major-version-support"></a>主要版本支持
如[版本控制策略](https://www.mysql.com/support/eol-notice.html)中所述，从 Azure 开始支持该版本之日起，到 MySQL 社区停用该版本之日结束，在此期间，Azure Database for MySQL 将支持 MySQL 的每个主要版本。

## <a name="minor-version-support"></a>次要版本支持
在定期维护过程中，Azure Database for MySQL 自动执行次要版本升级到 Azure 首选的 MySQL 版本。 

## <a name="major-version-retirement-policy"></a>主要版本停用策略
下表提供了 MySQL 主要版本的停用详细信息。 日期遵循 [MySQL 版本控制策略](https://www.mysql.com/support/eol-notice.html)。

| 版本 | 新增功能 | Azure 支持开始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [功能](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018 年 3 月 20 日 | 2021 年 2 月
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018 年 3 月 20 日 | 2023 年 10 月
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html) | 2019 年 12 月 11 日 | 2026 年 4 月


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Azure Database for MySQL 不支持已停用的 MySQL 引擎版本

在停用每个 MySQL 数据库版本之后，如果继续运行已停用版本，需要注意以下限制：
- 由于社区将不会发布任何进一步的 bug 修复或安全修复，Azure Database for MySQL 将不会针对任何 bug 或安全问题修补已停用的数据库引擎，也不会针对已停用的数据库引擎采取安全防护措施。 但是，Azure 将继续对主机、OS、容器以及任何其他与服务相关的组件执行定期维护和修补。
- 如果可能遇到的任何支持问题与 MySQL 数据库有关，我们可能无法为你提供支持。 在这种情况下，必须升级数据库，以便我们为你提供任何支持。
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- Azure Database for MySQL 开发的新服务功能可能仅适用于受支持的数据库服务器版本。
- 运行时间 SLA 仅适用于与 Azure Database for MySQL 服务相关的问题，而不适用于与数据库引擎相关的 bug 导致的任何故障时间。  
- 在极端情况下，如果已停用的数据库版本中识别的 MySQL 数据库引擎漏洞对服务造成严重威胁，Azure 可能会选择停止数据库服务器的计算节点，以首先保护服务。 系统将要求你在使服务器联机之前升级服务器。 在升级过程中，将始终使用在服务上执行的自动备份来保护数据，该备份可用于按需还原到旧版本。 



## <a name="next-steps"></a>后续步骤
- 请参阅 Azure Database for MySQL 单一服务器[支持的版本](./concepts-supported-versions.md)
- 请参阅 Azure Database for MySQL-灵活的服务器 (预览版) [支持的版本](flexible-server/concepts-supported-versions.md)
- 若要执行升级，请参阅 MySQL [转储和还原](./concepts-migrate-dump-restore.md)。
