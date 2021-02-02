---
title: 排查常见错误 - Azure Database for MySQL
description: 了解如何排查 Azure Database for MySQL 服务的新用户遇到的常见迁移错误
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ca75416a66bcf2c90028c7f1dc11fbe23a9a9bd9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631361"
---
# <a name="common-errors"></a>常见错误

Azure Database for MySQL 是由 MySQL 社区版本提供支持的完全托管的服务。 托管服务环境中的 MySQL 体验可能与在你自己的环境中运行 MySQL 时不同。 在本文中，你将了解用户首次迁移到 Azure Database for MySQL 服务或在该服务上开发时可能遇到的一些常见错误。

## <a name="common-connection-errors"></a>常见的连接错误

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>错误 1184 (08S01)：已中止到 db: 'db-name' 用户: 'user' 主机: 'hostIP' 的连接 22 (init_connect 命令失败)
建立会话时，如果已成功登录但尚未执行任何命令，则会出现上述错误。 上述消息表示你设置了错误的 init_connect 服务器参数值，这导致会话初始化失败。

有些服务器参数（如 require_secure_transport）在会话级别不受支持，因此尝试使用 init_connect 更改这些参数的值可能导致在连接到 MySQL 服务器时出现错误 1184，如下所示

mysql> 显示数据库; 错误 2006 (HY000):MySQL 服务器已断开连接。 正在尝试重新连接...连接 ID:  64897 当前数据库: *** 无 **_ 错误 1184 (08S01):已中止到 db: 'db-name' 用户: 'user' 主机: 'hostIP' 的连接 22 (init_connect 命令失败)

_ *解决方法**：应在 Azure 门户中重置“服务器参数”选项卡中的 init_connect 值，并使用 init_connect 参数仅设置支持的服务器参数。 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>由于缺少 SUPER 权限和 DBA 角色而引发的错误

该服务不支持 SUPER 权限和 DBA 角色。 因此，你可能会遇到下列的一些常见错误：

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>错误 1419：你没有 SUPER 权限且二进制文件日志记录已启用（你可能需要使用安全性更低的 log_bin_trust_function_creators 变量）

创建函数（如下所示触发）或导入架构时，可能会出现上述错误。 诸如 CREATE FUNCTION 或 CREATE TRIGGER 这样的 DDL 语句会写入二进制日志，因此次要副本可执行它们。 副本 SQL 线程具有完全权限，你可利用它来提升权限。 为了帮助启用了二进制日志记录的服务器防范这一危险，除了所需的惯例 CREATE ROUTINE 权限外，MySQL 引擎还要求存储函数创建者必须具有 SUPER 权限。 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**解决方法**：要解决此错误，请从门户中的 [服务器参数](howto-server-parameters.md)边栏选项卡将 log_bin_trust_function_creators 设置为 1，执行 DDL 语句或导入架构来创建所需的对象，并在创建后将 log_bin_trust_function_creators parameter 参数恢复到它之前的值。

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>第 101 行出现错误 1227 (42000)：访问被拒绝；需要（至少一项）SUPER 权限才能执行此操作。 操作失败，退出代码为 1

导入转储文件或创建包含[定义程序](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html)的过程时，可能会出现上述错误。 

**解决方法**：要解决此错误，管理员用户可通过运行 GRANT 命令来授予创建或执行过程的权限，如下例所示：

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
或者，可将定义程序替换为正在运行导入过程的管理员用户的姓名，如下所示。

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>第 295 行出现错误 1227 (42000)：访问被拒绝；需要（至少一项）SUPER 或 SET_USER_ID 权限才能执行此操作

在导入转储文件或运行脚本的过程中，执行 CREATE VIEW 和 DEFINER 语句时可能会发生上述错误。 Azure Database for MySQL 不允许任何用户使用 SUPER 特权或 SET_USER_ID 特权。 

**解决方法**： 
* 使用定义者用户执行 CREATE VIEW（如果可行）。 许多视图可能具有不同的定义者，而这些定义者拥有不同的权限，因此这可能不可行。  OR
* 编辑转储文件或 CREATE VIEW 脚本，然后从转储文件中删除 DEFINER= 语句，或者 
* 编辑转储文件或 CREATE VIEW 脚本，并将定义者值替换为拥有可执行导入或执行脚本文件的管理员权限的用户。

> [!Tip] 
> 使用 sed 或 perl 修改转储文件或 SQL 脚本以替换 DEFINER= 语句

## <a name="common-connection-errors-for-server-admin-login"></a>服务器管理员登录名的常见连接错误

创建 Azure Database for MySQL 服务器后，最终用户将在服务器创建过程中提供服务器管理员登录名。 服务器管理员登录名允许创建新数据库、添加新用户和授予权限。 如果删除了服务器管理员登录名、撤销了其权限或更改了其密码，则在连接时可能会开始在应用程序中看到连接错误。 下面是一些常见错误

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>错误 1045 (28000):拒绝访问用户“用户名”@“IP 地址”（使用密码：是）

如果出现以下情况，则会发生上述错误：

* 用户名不存在
* 用户用户名已删除
* 用户密码已更改或重置。

**解决方法**： 
* 验证“用户名”是作为服务器中的有效用户存在还是被意外删除。 可以通过登录到 Azure Database for MySQL 用户来执行以下查询：
  ```sql
  select user from mysql.user;
  ```
* 如果无法登录到 MySQL 以自行执行上述查询，建议[使用 Azure 门户重置管理员密码](howto-create-manage-server-portal.md)。 Azure 门户中的“重置密码”选项将帮助重新创建用户、重置密码和还原管理员权限，这将使你可以使用服务器管理员权限进行登录并执行进一步的操作。

## <a name="next-steps"></a>后续步骤
如果找不到所需的答案，请考虑以下操作：

- 在 [Microsoft Q&A 问题页](/answers/topics/azure-database-mysql.html)或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql) 上发布问题。
- 向 Azure Database for MySQL 团队 ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)) 发送电子邮件。 此电子邮件地址不是技术支持别名。
- 若要联系 Azure 支持人员，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 创建条目。
